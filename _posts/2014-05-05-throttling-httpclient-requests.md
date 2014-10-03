---
layout: post
title: "Throttling HttpClient requests"
date: 2014-05-05 15:43
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Development]
share: true
---
In my [previous post]({% post_url 2014-05-02-throttling-web-requests %} "Throttling web requests"), I've introduced a TimeSpanSemaphore,
that functions as a throttling "lock". Let's see how we can use that semaphore to throttle HttpClient requests. The naive solution would be to create
an instance of the semaphore right next to the [HttpClient](http://msdn.microsoft.com/en-us/library/system.net.http.httpclient(v=vs.118).aspx), and
"wrap" each request with a wait/release call on the semaphore:

{% highlight csharp %}
HttpClient client = new HttpClient();
TimeSpanSemaphore semaphore = new TimeSpanSemaphore(10, TimeSpan.FromSeconds(1));
try
{
	semaphore.Wait();
	client.GetAsync(...);
}
finally
{
	semaphore.Release();
}
{% endhighlight %}

As you can see, this can become quite tedious, and with the try-finally blocks, quire redundant. Luckily for
us, [HttpClient](http://msdn.microsoft.com/en-us/library/system.net.http.httpclient(v=vs.118).aspx) contains an extendibility point that allows
use to add our own custom code in the request pipeline - handlers. We'll start by creating our custom handler:

{% highlight csharp %}
public class ThrottlingMessageHandler : DelegatingHandler
{
	private readonly TimeSpanSemaphore _timeSpanSemaphore;
	
	public ThrottlingMessageHandler(TimeSpanSemaphore timeSpanSemaphore)
		: this(timeSpanSemaphore, null)
	{ }

	public ThrottlingMessageHandler(TimeSpanSemaphore timeSpanSemaphore, HttpMessageHandler innerHandler)
		: base(innerHandler)
	{
		_timeSpanSemaphore = timeSpanSemaphore;
	}

	protected override Task SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
	{
		return _timeSpanSemaphore.RunAsync(base.SendAsync, request, cancellationToken);
	}
}
{% endhighlight %}

As you can see, our handler accept a TimeSpanSemaphore in the constructor. If you are communicating with a single service, you might not need that, 
and you can change the handler to use an internal TimeSpanSemaphore. The handler can also accept an inner handler, which is used to create a chain of
handlers, as you will see below. The overloaded method SendAsync contains the interesting part - it wraps the call to base.SendAsync with the
semaphore's RunAsync method, which will first call Wait on the semaphore (which will lock if too many requests were made in the specified time frame),
then it will call base.SendAsync (which will perform the actual request). Finally, it will call Release (to notify the semaphore that the request
is completed). We not only need to change the code that creates our HttpClient:

{% highlight csharp %}
var handler = new HttpClientHandler();
if (handler.SupportsAutomaticDecompression)
	handler.AutomaticDecompression = DecompressionMethods.Deflate | DecompressionMethods.GZip;
	
var throttlingHandler = new ThrottlingMessageHandler(ThrottlingSemaphore, handler);
var client = new HttpClient(throttlingHandler );
{% endhighlight %}

The code above creates an [HttpClientHandler](http://msdn.microsoft.com/en-us/library/system.net.http.httpclienthandler(v=vs.118).aspx) (and enables
automatic decompression on it). It then wrap that handler with out new ThrottlingMessageHandler. After that, we can just create a new HttpClient,
and pass along our handler.