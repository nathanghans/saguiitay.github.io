---
title: "MarkedUp release beta support for WP8"
date: 2013-06-14 16:58
author: saguiitay
categories: [Development, Windows Phone]
tags: [MarkedUp, Windows Phone]
redirect_from:
 - /2013-06-14-markedup-release-beta-support-for-wp8/
 - /development/markedup-release-beta-support-for-wp8/
---
[MarkedUp](https://markedup.com/), the company that provides analytic tools for Windows 8 (WinRT & WinJS), released their SDK with Windows Phone 8 support.

To install MarkedUp Analytics SDK for Windows Phone 8 (BETA), run the following command in the [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)

{% highlight powershell %}
PM> Install-Package MarkedUp-wp8beta -Pre
{% endhighlight %}

Keep in mind, MarkedUp is still in beta for Windows Phone 8, and here are the known issues with it:

- All commerce events are currently disabled
- LogLastChanceException (automatic crash logging) currently doesn't work
- The networking layer is a little slow to respond to changes in network availability when transitioning from a disconnected state to connected one.
