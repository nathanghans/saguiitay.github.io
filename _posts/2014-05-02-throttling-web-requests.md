---
layout: post
title: "Throttling web requests"
date: 2014-05-02 15:42
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Cloudsfer]
share: true
redirect_from:
 - /2014/05/02/throttling-web-requests/
 - /2014-05-02-throttling-web-requests/
 - /development/throttling-web-requests/
---
As part of my work on [Cloudsfer](http://www.cloudsfer.com), I'm working a lot with REST APIs of various cloud service providers, such as
OneDrive, Dropbox and Instagram. Most of these services limit you to a certain amount of requests per second/hour/day. This is understood - these
services need to protect themselves from DDOS attacks, and other abuses. If you make too many calls to these APIs, they will start returning
errors (either as part of the response, or as an HTTP Status Code). Some services, such as OneDrive, will include some information about
when you should retry your request (either as part of the response, or perhaps as an HTTP header). In order to avoid such cases, you need
to control the number of requests made in each time window. Unfortunately, the CLR/.Net doesn't provide any built-in locking mechanism for
such a behavior.

After some searching, I've found a [TimeSpanSemaphore implementation](http://joelfillmore.com/throttling-web-api-calls/), written by [Joel Fillmore](http://joelfillmore.com/),
that does just that - locks if too many requests are made in the specified timeframe.

{% highlight csharp %}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Diagnostics;

namespace JFLibrary
{
	/// 
	/// Allows a limited number of acquisitions during a timespan
	/// 
	public class TimeSpanSemaphore : IDisposable
	{
		private SemaphoreSlim _pool;

		// the time span for the max number of callers
		private TimeSpan _resetSpan;

		// keep track of the release times
		private Queue _releaseTimes;

		// protect release time queue
		private object _queueLock = new object();

		public TimeSpanSemaphore(int maxCount, TimeSpan resetSpan)
		{
			_pool = new SemaphoreSlim(maxCount, maxCount);
			_resetSpan = resetSpan;

			// initialize queue with old timestamps
			_releaseTimes = new Queue(maxCount);
			for (int i = 0; i < maxCount; i++)
			{
				_releaseTimes.Enqueue(DateTime.MinValue);
			}
		}

		/// 
		/// Blocks the current thread until it can enter the semaphore, while observing a CancellationToken
		/// 
		private void Wait(CancellationToken cancelToken)
		{
			// will throw if token is cancelled
			_pool.Wait(cancelToken);

			// get the oldest release from the queue
			DateTime oldestRelease;
			lock (_queueLock)
			{
				oldestRelease = _releaseTimes.Dequeue();
			}

			// sleep until the time since the previous release equals the reset period
			DateTime now = DateTime.UtcNow;
			DateTime windowReset = oldestRelease.Add(_resetSpan);
			if (windowReset > now)
			{
				int sleepMilliseconds = Math.Max(
					(int)(windowReset.Subtract(now).Ticks / TimeSpan.TicksPerMillisecond),
					1); // sleep at least 1ms to be sure next window has started
				Debug.WriteLine("Waiting {0} ms for TimeSpanSemaphore limit to reset.", sleepMilliseconds);

				bool cancelled = cancelToken.WaitHandle.WaitOne(sleepMilliseconds);
				if (cancelled)
				{
					Release();
					cancelToken.ThrowIfCancellationRequested();
				}
			}
		}

		/// 
		/// Exits the semaphore
		/// 
		private void Release()
		{
			lock (_queueLock)
			{
				_releaseTimes.Enqueue(DateTime.UtcNow);
			}
			_pool.Release();
		}

		/// 
		/// Runs an action after entering the semaphore (if the CancellationToken is not canceled)
		/// 
		public void Run(Action action, CancellationToken cancelToken)
		{
			// will throw if token is cancelled, but will auto-release lock
			Wait(cancelToken);

			try
			{
				action();
			}
			finally
			{
				Release();
			}
		}

		/// 
		/// Releases all resources used by the current instance
		/// 
		public void Dispose()
		{
			_pool.Dispose();
		}
	}
}
{% endhighlight %}

