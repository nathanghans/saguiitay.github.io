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

## Copy REST API

CopyRestAPI is a C# client for the Copy cloud storage service through its RESTful API.

Copy haven't released an official .Net/C# library, but provide some third-party alternative, which can be found in their documentation page <https://developers.copy.com/libraries>

<https://github.com/saguiitay/CopyRestAPI>


## Dropbox REST API

DropboxRestAPI is a C# client for Dropbox service through its RESTful API, with support for both the Core and the Business APIs.

Dropbox haven't released an official .Net/C# library, but there are several alternatives, including mine, which can be found in their documentation page <https://www.dropbox.com/developers/core/sdks/other>

<https://github.com/saguiitay/DropboxRestAPI>

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