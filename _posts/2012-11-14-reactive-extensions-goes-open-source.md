---
title: "Reactive Extensions goes Open Source"
date: 2012-11-14 20:32
author: saguiitay
categories: [Development]
tags: [.Net, C#, Reactive Extensions]
redirect_from:
 - /2012-11-14-reactive-extensions-goes-open-source/
 - /development/reactive-extensions-goes-open-source/
---
With Reactive Extensions going open-source, as mentioned in [Scott Hanselman's "*Reactive Extensions (Rx) is now Open Source*"](http://www.hanselman.com/blog/ReactiveExtensionsRxIsNowOpenSource.aspx)
post, I took a swing at the library, as it solved one of my requirements perfectly. Having the ability to [buffer](http://www.introtorx.com/Content/v1.0.10621.0/13_TimeShiftedSequences.html#Buffer) 
events (or "[subjects](http://msdn.microsoft.com/en-us/library/hh229173(v=VS.103).aspx)"), and process them in chunks, 
while keeping a time-limit on the delay is extremely simple:

{% highlight csharp %}
static void Main()
{
	// Define a "stream" of objects
	var items = new Subject();

	items
		// Buffer the stream into chunks of 5, yet process whatever you get after a maximum of 10 seconds
		.Buffer(TimeSpan.FromSeconds(10), 5)
		// Process the buffered items
		.Subscribe(buffer =>
						{
							foreach (var item in buffer)
								Console.WriteLine("\tProccessed {0}", item);
						});

	string line;
	while (!string.IsNullOrEmpty(line = Console.ReadLine()))
	{
		items.OnNext(line);
	}

	// Notify the stream that there are no more objects, so the remaing buffered objects are processed immediately
	items.OnCompleted();
}
{% endhighlight %}

The following links proved extremely helpful to get me started quickly:

- [Reactive Framework (Rx) Wiki](http://rxwiki.wikidot.com/)
- [Introduction To Rx](http://www.introtorx.com/)
