---
title: "HyperLogLog.net "
excerpt: "A near-optimal cardinality estimation algorithm implemented in C#."
author_profile: false
header:
  teaser: github-logo.png
sidebar:
  - title: 
    text: "[GitHub](https://github.com/saguiitay/HyperLogLog.net){: .btn .btn--large .btn--success}"
  - title: 
    text: "[NuGet](https://www.nuget.org/packages/HyperLogLog.net/){: .btn .btn--large .btn--success}"
---

HyperLogLog.net is a C# implementation of HyperLogLog - a near-optimal cardinality estimation algorithm.

HLL was conceived of by Flajolet et. al. in the phenomenal paper [HyperLogLog: the analysis of a near-optimal cardinality estimation algorithm](http://algo.inria.fr/flajolet/Publications/FlFuGaMe07.pdf).

A visual demonstration of HyperLogLog is available [here](http://content.research.neustar.biz/blog/hll.html) along with a [basic explanation](http://research.neustar.biz/2012/10/25/sketch-of-the-day-hyperloglog-cornerstone-of-a-big-data-infrastructure/) about it.

## Nuget Package

To install WebDAVClient, run the following command in the Package Manager Console

```
Install-Package HyperLogLog.net  
```

## Features

  - Support custom `HashAlgorithms`. (Default is MD5, but I strongly recommend using Murmur3 from [murmurhash](https://www.nuget.org/packages/murmurhash/)
  - Support custom `byte[]` converters. (Default handles value types, and calls `ToString()` on all other types)
  - Support serialization into/from `byte[]` for network transfer
  - Allows merging multiple HyperLogLog objects into a single HyperLogLog. Useful if you are handling a high volume of data on multiple machines, and needs to aggregate the data for a "final" number.