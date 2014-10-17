---
layout: post
title: "MurmurHash - a FAST hashing algorithm"
date: 2014-10-17 16:58
author: saguiitay
comments: true
categories: [Development]
tags: [.Net]
share: true
redirect_from:
  - /2014/10/17/murmurhash-a-fast-hashing-algorithm/
  - /2014-10-17-murmurhash-a-fast-hashing-algorithm/
  - /2014-10-17-murmurhash-a-fast-hashing-algorithm
---
As I did my research on [HyperLogLog]({% post_url 2014-10-16-hyperloglog-a-near-optimal-cardinality-estimation-algorithm %}), I used the built-in MD5 `HashAlgorithm`.
As I was running my tests on larger and larger datasets, the running time grew too much to my liking.

Since HyperLogLog doesn't require a cryptographic hash function, MD5 is a bit of an overkill. I went hunting for a faster option. This is when I encountered MurmurHash.

MurmurHash is a non-[cryptographic hash function](http://en.wikipedia.org/wiki/Cryptographic_hash_function) suitable for general hash-based lookup. Luckily for me,
there's already a very comprehensive implementation of it in .Net, with the source code available in GitHub (<https://github.com/darrenkopp/murmurhash-net>) and
as a NuGet package (<https://www.nuget.org/packages/murmurhash/>).

Murmur3 turned out to be much faster than MD5, allowing me to run my tests with much large datasets, and faster running time.

While Murmur3 doesn't have too many collisions, it is not designed to be difficult to reverse by an adversary, making it unsuitable for cryptographic purposes.

