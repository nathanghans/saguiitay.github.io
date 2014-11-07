---
layout: page
permalink: /open-source-projects/
title: "Open Source Projects"
date: 2014-11-07 13:00
author: saguiitay
categories: []
tags: []
---

# Projects

## OneDrive REST API

OneDriveRestAPI is a C# client for the Microsoft OneDrive service through its RESTful API.

Microsoft has released the Live SDK, a set of C# libraries for interacting with OneDrive, which is hard to extend and debug, as it relies on async
HttpWebRequest/HttpWebResponse.

<https://github.com/saguiitay/OneDriveRestAPI>

## WebDAV REST API

Strongly-typed, async WebDAV Client implementation in C#.

WebDAVClient is based originally on https://github.com/kvdb/WebDAVClient. I've added Async support (instead of Callback), as well strong-types responses.

<https://github.com/saguiitay/WebDAVClient>

## HyperLogLog.net 

HyperLogLog.net is a C# implementation of HyperLogLog - a near-optimal cardinality estimation algorithm.

HLL was conceived of by Flajolet et. al. in the phenomenal paper [HyperLogLog: the analysis of a near-optimal cardinality estimation algorithm](http://algo.inria.fr/flajolet/Publications/FlFuGaMe07.pdf).

A visual demonstration of HyperLogLog is available [here](http://content.research.neustar.biz/blog/hll.html) along with a [basic explanation](http://research.neustar.biz/2012/10/25/sketch-of-the-day-hyperloglog-cornerstone-of-a-big-data-infrastructure/) about it.

<https://github.com/saguiitay/HyperLogLog.net>