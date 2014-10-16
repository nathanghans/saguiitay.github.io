---
layout: post
title: "Error while using HttpClient's AutomaticDecompression in Portable Libraries"
date: 2013-08-23 12:45
author: saguiitay
comments: true
categories: [Development]
tags: [C#, HttpClient, Portable Library]
share: true
redirect_from:
 - /2013/08/23/error-while-using-httpclients-automaticdecompression-in-portable-libraries/
---
Adding automatic decompression support to an [HttpClient](http://msdn.microsoft.com/en-us/library/system.net.http.httpclient.aspx) is quite easy:

{% highlight csharp %}
var handler = new HttpClientHandler();
if (handler.SupportsAutomaticDecompression)
	handler.AutomaticDecompression = DecompressionMethods.Deflate | DecompressionMethods.GZip;

var httpClient = new HttpClient(handler);
{% endhighlight %}

This works even in Portable Libraries. However, if you try to call this from a different project, you might encounter the strange exception below:

> Method not found: `Void System.Net.Http.HttpClientHandler.set_AutomaticDecompression(System.Net.DecompressionMethods)`.

This doesn't give to much information - for some reason, the setter of the AutomaticDecompression property was not found,
which prevent the code from JIT'ing. Apparently, the solution for that is quite simple - you need to make sure that the project that calls the
Portable Library also refers to the [Microsoft.Net.Http Nuget package](http://www.nuget.org/packages/Microsoft.Net.Http/).
