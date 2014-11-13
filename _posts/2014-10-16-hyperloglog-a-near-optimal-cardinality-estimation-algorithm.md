---
layout: post
title: "HyperLogLog - a near-optimal cardinality estimation algorithm"
date: 2014-10-16 18:58
author: saguiitay
comments: true
categories: [Development]
tags: [.Net]
share: true
redirect_from:
 - /2014/10/16/hyperloglog-a-near-optimal-cardinality-estimation-algorithm/
 - /2014-10-16-hyperloglog-a-near-optimal-cardinality-estimation-algorithm/
 - /development/hyperloglog-a-near-optimal-cardinality-estimation-algorithm/
---
Over the last several months I've been immersing myself further and further into the big data world, and into developing and operating a SaaS. I've been trying to
expose myself to as much information as possible - both from a business and product management point of view, as well as from a development and operation of such
platform.

Yesterday I've encountered HyperLogLog - a near-optimal cardinality estimation algorithm.

Let's explain that phrase, then we'll continue:

- Near Optimal: the algorithm is efficient, has a low memory footprint, and provide greater accuracy (compared to other algorithms)
- [Cardinality](http://en.wikipedia.org/wiki/Cardinality): counting the number of elements in a set. This algorithm helps you count the number of items you handled
it (and it does it in an efficient manner, without keeping the list of all previous encountered items, thus the low memory footprint)
- Estimation: the algorithm doesn't provide an exact result, but is quite accurate in most cases (less than 2% margin of error)

The algorithm was conceived of by Flajolet et. al. in the paper [HyperLogLog: the analysis of a near-optimal cardinality estimation algorithm](http://algo.inria.fr/flajolet/Publications/FlFuGaMe07.pdf), 
which in turn is based on [FM-85](http://algo.inria.fr/flajolet/Publications/FlMa85.pdf), Flajolet and Martin’s original work on probabilistic counting.

I've always wondered how these kind of this - counting big data events, such as the number of unique Google queries - are implemented. Not only the number of possible
events is boundless, the very nature of these kind of questions is distributed.

After reading the HyperLogLog paper, and playing with the [visualization tool](http://content.research.neustar.biz/blog/hll.html), I've decided to see if there's a C#
implementation for HyperLogLog. I've found bits and pieces - [MarkedUp](http://www.markedup.com) have the [beginning of an implementation](https://github.com/markedup-mobi/openmetrics),
[Artem Tartakynov](https://github.com/tartakynov) has a [simple implementation](https://github.com/tartakynov/hyperloglog).

However, I couldn't find a complete implementation that met some of my requirements:

- Support multiple hashing algorithms
- Can be extended to work with any type of objects
- Is simple to understand and extend as needed

As usual lately, I ended up implementing my own code - <https://github.com/saguiitay/HyperLogLog.net> (also available as a NuGet package <https://www.nuget.org/packages/HyperLogLog.net/>)

After I finished with the implementation, I've also reviewed the [Google suggested improvements](http://research.google.com/pubs/pub40671.html). I've implemented their
first suggestion, of extending the hash size from 32bits to 64bits, in order to reduce chances for collisions when handling a large number of events. They also
suggested using sparce arrays, which I might implement later on).

Their final suggestion was in improving the estimation made by the algorithm when handling a small number of events (the algorithm is not very accurate in those cases).
I decided against implementing this improvement, since it relies heavily on a lookup table for "correcting" the algorithm's estimation. Although I don't mind the lookup
tables themselves, they require more memory, which seems counter intuitive to me at this point.

As usual, I welcome feedback and any pull-requests you might have.
