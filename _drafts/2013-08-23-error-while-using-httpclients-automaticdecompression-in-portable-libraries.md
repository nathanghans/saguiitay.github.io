---
layout: post
title: "Error while using HttpClient's AutomaticDecompression in Portable Libraries"
date: 2013-08-23 12:45
author: saguiitay
comments: true
categories: [.Net, Development]
tags: [c#, httpclient, portable-library]
---
Adding automatic decompression support to an [HttpClient](http://msdn.microsoft.com/en-us/library/system.net.http.httpclient.aspx) is quite easy:

Code Snippet

<span style="background:#ffffff;color:#0000ff;">var</span><span style="background:#ffffff;color:#000000;"> handler = </span><span style="background:#ffffff;color:#0000ff;">new </span><span style="background:#ffffff;color:#2b91af;">HttpClientHandler</span><span style="background:#ffffff;color:#000000;">();</span> <span style="background:#ffffff;color:#0000ff;">if</span><span style="background:#ffffff;color:#000000;"> (handler.SupportsAutomaticDecompression)</span> <span style="background:#ffffff;color:#000000;">    handler.AutomaticDecompression = </span><span style="background:#ffffff;color:#2b91af;">DecompressionMethods</span><span style="background:#ffffff;color:#000000;">.Deflate | </span><span style="background:#ffffff;color:#2b91af;">DecompressionMethods</span><span style="background:#ffffff;color:#000000;">.GZip;</span> <span style="background:#ffffff;color:#0000ff;">var</span><span style="background:#ffffff;color:#000000;"> httpClient = </span><span style="background:#ffffff;color:#0000ff;">new </span><span style="background:#ffffff;color:#2b91af;">HttpClient</span><span style="background:#ffffff;color:#000000;">(\_handler);</span>

This works even in Portable Libraries. However, if you try to call this from a different project, you might encounter the strange exception below:

*Method not found: ‘Void System.Net.Http.HttpClientHandler.set\_AutomaticDecompression(System.Net.DecompressionMethods)’.*

This doesn't give to much information - for some reason, the setter of the AutomaticDecompression property was not found, which prevent the code from JIT'ing. Apparently, the solution for that is quite simple - you need to make sure that the project that calls the Portable Library also refers to the [Microsoft.Net.Http Nuget package](http://www.nuget.org/packages/Microsoft.Net.Http/).


