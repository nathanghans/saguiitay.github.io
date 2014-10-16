---
layout: post
title: "NetFlix compressed graphs in .Net"
date: 2014-10-15 15:58
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, NetFlix, graphs]
share: true
redirect_from:
 - /2014/10/15/netflix-compressed-graphs-in-net/
---
A while back, NetFlix wrote in their [blog about their graph library](http://techblog.netflix.com/2013/01/netflixgraph-metadata-library_18.html), and the work
they've done to reduce the memory footprint of their graph. Reducing memory allows NetFlix to maintain the whole graph relations in-memory (note that the node
themselves are not maintained in-memory), which means that queries on the graph are returned extremely fast. The low memory footprint means that you can use this
solution even in smaller devices with lower memory available (such as low-end smart phones, smart TVs, etc.).

I've always been interested in graphs, especially since I've used them to create my series of Trivia games ([Movies]({{site.url}}/windows-phone/movies-trivia),
[Geo]({{site.url}}/windows-phone/geo-trivia), [The Beatles]({{site.url}}/windows-phone/the-beatles-trivia) and
[Red Hot Chilli Peppers]({{site.url}}/windows-phone/rhcp-trivia)). I've even written about my usages of the graphs in those games [here]({{site.url}}/windows-phone/).

I've decided I'd try to convert NetFlix's code to .Net, and see if it works, and what will be the results.

Here's are two ways to represent a graph of movies, actors and ratings - the fist using an Object Oriented approach, the other with a more graph-centered approach:

### Object Oriented approach

Each object contains references to its related objects:

{% highlight csharp %}
public class Movie {
	public string Name { get; set; }
	public Rating Rating { get; set; } // Simple one-to-one relation
	public List<Actor> Actors { get; set; } // One-to-many relation
}

public class Actor {
	public string Name { get; set; }
	public Hashset<Actor> CostarredWith { get; set; }
	public List<Movie> StarredIn { get; set; }
}

public class Rating {
	public string Rating { get; set; }
}
{% endhighlight %}

### Relations/Graph centric approach

A "centralized" object, which represent the graph as a whole, contains the relations between objects:

{% highlight csharp %}
public class Graph {
	public Dictionary<Movie, Rating> MoviesRatings { get; set; }
	public Dictionary<Movie, List<Actor>> MoviesActors { get; set; }
	public Dictionary<Actor, List<Actor>> ActorsCostarredWithActors { get; set; }
}
{% endhighlight %}

Both these methods force you to either keep the whole object model in memory, or keep querying the database to get the information.

NetFlix's approach relies on several "mechanisms" to improve the work with the model:

* Objects are kept anywhere you want, as long as you provide a unique Ordinal Index - the "index" of the object within the collection of objects of the same
type. If you're using a database, that can be the index of the row.
* Relations are represented as collections of indexes, and not references to the actual objects - if Movie #3 is related to Actors #17, #235 and #19, this
is represented as \[17, 235, 19\]
* Relations are grouped by type ("Actor starred in movie", "Movie is rated X"), and merged in to a single array of indexes. So if the above Movie #3 is rated using
rating #5 (say "PG13"), all relations are represented as \[0, 17, 235, 19, 1, 5\] (the "0" and "1" represent the type of the relation).
* Each group of relations is sorted, and converted to delta-based values. The previous array becomes \[0, 17, 2, 216, 1, 5\].
* The previous steps means that we are working with smaller numbers, which means that we can save them in-memory in less bytes, using [Variable-length quantity](http://en.wikipedia.org/wiki/Variable-length_quantity)

In addition, there are a couple of extra tricks up their sleeves:

* If a certain type of objects contains only a small number of items (for example, ratings might be limited to R, PG13, PG16 and E), instead of keeping an index of
relations for each object, you can use a BitArray - use a single bit to notify if a relation exists or not.
* Some relations are global, and others are "model"-based. For example, "Keanu Reeves" played in "The Matrix" no matter where you are in the world, but "The Matrix"'s
rating might be different in every country.

As you can see, a lot of thought went into reducing the memory usage of the graph as much as possible.

I've put my converted code in [GitHub](https://github.com/saguiitay/netflix-graph.net "netflix-graph.net"). Feel free to give it a shot, send me feedback or pull
requests. I'll try playing with it further myself, and see what can be achieved with it.


