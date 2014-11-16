---
layout: post
title: "Byte Rot: Performance Counters for your HttpClient"
date: 2014-09-30 21:07
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, HttpClient]
share: true
redirect_from:
 - /2014-09-30-byte-rot-performance-counters-for-your-httpclient/
 - /development/byte-rot-performance-counters-for-your-httpclient/
---
I've been an avid user of [HttpClient](http://msdn.microsoft.com/en-us/library/system.net.http.httpclient(v=vs.118).aspx) over the last couple of years.
You can see me using them in my various Windows Phone applications ([Using the LibraryThing API from C#]({% post_url 2014-09-24-using-the-librarything-api-from-c %}),
and in my work at Cloudsfer ([Throttling HttpClient requests]({% post_url 2014-05-05-throttling-httpclient-requests %}) and 
[Accessing OneDrive via .Net]({% post_url 2014-08-20-accessing-onedrive-via-net %})).

As you can imagine, I always love to find new functionalities related to HttpClient, and new ways to improve my work with it.

In his blog, [Byte Rot](http://byterot.blogspot.com), [@aliostad](https://twitter.com/aliostad) shows a way to monitor HttpClient requests using
PerformanceCounters, via a [DelegatingHandler](http://msdn.microsoft.com/en-us/library/system.net.http.delegatinghandler(v=vs.118).aspx).

Although I refrain from using PerformanceCounters in my code, due to some [bad experience](http://ayende.com/blog/165217/performance-counters-sucks) with them,
I do like his clean and simple approach. The use of DelegatingHandler provides a nice separation of concerns, and I can easily see someone
use it with my [OneDrive REST API](https://github.com/saguiitay/OneDriveRestAPI) library (pull requests are welcome).
