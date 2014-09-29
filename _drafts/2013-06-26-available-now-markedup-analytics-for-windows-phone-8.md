---
layout: post
title: "Available Now: MarkedUp Analytics for Windows Phone 8"
date: 2013-06-26 10:32
author: saguiitay
comments: true
categories: [Development, Windows Phone]
tags: [.Net, development, markedup, Windows Phone]
---
MarkedUp have released their Analytics library for Windows Phone 8 - you can read the announcement on their [blog](http://blog.markedup.com/2013/06/available-now-markedup-analytics-for-windows-phone-8/) MarkedUp Analytics gives you insight on unique users, sessions, time spend in your application, crashes, and even custom events - all invaluable information for app developers! Using MarkedUp Analytics is extremely simple - after registration, you can register an application, and get a unique API Key. After that you just need to add their [NuGet package](https://nuget.org/packages/MarkedUp/) into your project:

    Install-Package MarkedUp

And then execute the following code from your Initialize method:

    MarkedUp.AnalyticClient.Initialize("Your-API-Key");

It's as simple as that!


