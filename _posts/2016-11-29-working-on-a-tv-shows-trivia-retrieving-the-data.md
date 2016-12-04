---
title: "Woring on a TV shows trivia game - retrieving the data"
date: 2016-11-29 22:56
author: saguiitay
categories: [Development]
tags: [.Net]
---

In our [previous post](http://www.saguiitay.com/2016/11/27/working-on-a-tv-shows-trivia-game/) we discussed about representing TV shows data as a graph using objects and their relations.

In this post, we'll see how to gather the information needed for our game.

There are a few websites that allows you to follow up on the show you watch, such as [Rotten Tomatoes](https://www.rottentomatoes.com/tv/) and [TheTvDB](http://thetvdb.com/). I've decided to start my
work with [TvMaze](http://www.tvmaze.com/), mainly because they have a [simple API](http://www.tvmaze.com/api) that easy easy to use, and which provide me with a good starting point for my data.

Let's start by defining a new class:

{% highlight csharp %}
public class ShowListing
{
    public int id { get; set; }
    public string name { get; set; }
}
{% endhighlight %}

With that, we can start retrieving the list of shows from TvMaze:

{% highlight csharp %}
var hasMore = true;
var pageNum = 6;
var webClient = new WebClient();
do
{

    string url;
    if (pageNum > 0)
    {
        url = $"http://api.tvmaze.com/shows?page={pageNum}";
    }
    else
    {
        url = $"http://api.tvmaze.com/shows";
    }
    var data = webClient.DownloadString(new Uri(url));
    var pagedSearch = JsonConvert.DeserializeObject<ShowListing[]>(data);

    hasMore = pagedSearch.Length > 0;

    foreach (var serie in pagedSearch)
    {
        Console.WriteLine($"Show: {serie.name} (Id: {serie.id}");
    }
    pageNum++;

    // We'll add a delay, so as not to overload the TvMaze servers and get throttled
    Thread.Sleep(100);
} while (hasMore);
{% endhighlight %}

We now have a list of TV shows, and their Id. You can find the object model I've created using Visual Studio's
"Paste Special" feature in the following Gist:

{% gist saguiitay/08b708bdf1ab0e0b0c07a9b1af95045e %}

We can now retrieve the information about a serie based on it's name or id:

{% highlight csharp %}
// Use one of the following
string name = "";
string id = "";

string url;
if (!string.IsNullOrEmpty(name))
{
    url = $"http://api.tvmaze.com/singlesearch/shows?q={name}&embed[]=cast&embed[]=seasons";
}
else
{
    url = $"http://api.tvmaze.com/shows/{id}?embed[]=cast&embed[]=seasons";
}
var data = webClient.DownloadString(new Uri(url));

var singleSearch = JsonConvert.DeserializeObject<SingleSearch>(data);
{% endhighlight %}

From here, it's just about retrieving the information we need:

  * `singleSearch.premiered` Year when show premiered
  * `singleSearch._embedded`
    * `singleSearch._embedded.cast` List of cast
        * `singleSearch._embedded.cast[].person` Actor playing character 
            * `singleSearch._embedded.cast[].person.image` Image of the actor
        * `singleSearch._embedded.cast[].character` Character 
            * `singleSearch._embedded.cast[].character.image` Image of the character
    * `singleSearch._embedded.seasons` List of seasons
        * `singleSearch._embedded.season[].image` Image of season

In the next post, we'll see where we can get taglines and quotes for the shows.