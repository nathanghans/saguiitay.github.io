---
layout: post
title: "Accessing OneDrive via .Net"
date: 2014-08-20 15:34
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, NuGet, OneDrive, REST, SkyDrive]
share: true
redirect_from:
 - /2014/08/20/accessing-onedrive-via-net/
 - /2014-08-20-accessing-onedrive-via-net/
 - /2014-08-20-accessing-onedrive-via-net
---
TLDR; I created a new [NuGet package](https://www.nuget.org/packages/OneDriveRestAPI)/[GitHub repository](https://github.com/saguiitay/OneDriveRestAPI) for a
library to allow you access to OneDrive via .Net.

Over the last few days, I've implemented a .Net Class library to provide .Net access to OneDrive. Although Microsoft provides its own SDK (which is
found at <http://msdn.microsoft.com/en-US/live/ff621310>), that library is not very developer friendly - it's hard to debug, and harder to extend.
I've search for alternatives on the internet, but the only usable solution I've found was [John Hoerr](https://twitter.com/johnhoerr)'s
[SkyNet](https://github.com/jhoerr/SkyNet) project. Although useful as a start, I've encountered several issues with it:

- It didn't support Async patterns
- It's support for downloading/uploading file was limited (mostly because it relies on RestSharp)
- It's Object Model wasn't complete (some objects and methods were missing)
- It wasn't updated in a while

I've decided to implement my own library. I've started with the SkyNet repository. The first thing I did was replace all [RestSharp](http://restsharp.org/)
code with simpler HttpClient (from the [Microsoft.Net.Http](https://www.nuget.org/packages/Microsoft.Net.Http) NuGet).
This meant that I had to implement a simplified version of the [Request](https://github.com/saguiitay/OneDriveRestAPI/blob/master/src/OneDriveRestAPI/Util/Request.cs)
object. Nothing too complex - just enough to allow me to organize the code in a sensible manner.

That also forced me to modify all method to Async, which was great, since I planned to do that anyway. Then I've added a few models,
such as Error and UserProfile, along with their relevant methods. After that, came some extendibility requirements. The code that I currently
work on (and which will use this Library) makes quite a few requests to OneDrive. I needed a way to throttle all these requests. For the moment,
I implemented this by providing callers the ability to wrap the HttpMessageHandler that the HttpClient is using.
Thus I'm able to use a [TimeSpanSemaphore]({% post_url 2014-05-02-throttling-web-requests %} "Throttling web requests") to throttle all requests.

I've also decided to implement auto-token-refresh. I'm using OneDriveRestAPI in a long-running process. That means that the AccessToken returned
by OneDrive might expire during my processing. Using a RefreshToken, the library automatically retrieve an updated AccessToken (as long as the
user didn't revoke permissions on OneDrive).

You are of course welcome to send me any feedback, fork and repository, and send pull requests. I'll try to keep this up-to-date as much as possible.
