---
layout: post
title: "Bloomberg's Billionaires - Part 2: General data and stats"
date: 2013-06-21 19:58
author: saguiitay
comments: true
categories: [Development]
tags: [Billionaires, Windows Phone]
share: true
redirect_from:
 - /2013/06/21/bloombergs-billionaires-part-2-general-data-and-stats/
 - /2013-06-21-bloombergs-billionaires-part-2-general-data-and-stats/
 - /2013-06-21-bloombergs-billionaires-part-2-general-data-and-stats
---
In my [previous post]({% post_url 2013-06-17-bloomberg-billionaires %} "Bloomberg Billionaires"), I've mentioned Bloomberg's Billionaires website,
which displays detailed information about the Top 100 world billionaires. Today, let's take a look at the data used by the website.

Using Fiddler, we can browse to (http://www.bloomberg.com/billionaires/) and follow the AJAX requests performed by the page.

The first interesting piece of information is a request to (http://www.bloomberg.com/billionaires/db/metadata/js/) - this request gives us a
nice JSON (ignoring the "META=" part at the beginning of the content). A quick review of the JSON reveals that it contains the following information:

- Collection of Scopes (I'm not sure what's the purpose of this yet)
- Collection of Places - this is a list of places/countries that have Billionaires)
- Collection of People - this is the important part for us. For each person, we have some basic global information such as name, age, industry.

Here's the class that represent a single person in the "people" collection:

{% highlight csharp %}
public class Name
{
	public string full { get; set; }
	public string last { get; set; }
	public string sort { get; set; }
}

public class Person
{
	public Name name { get; set; }
	public int age { get; set; }
	public int children { get; set; }
	public string status { get; set; }
	public string place { get; set; }
	public string gender { get; set; }
	public string industry { get; set; }
	public string source { get; set; }
	public string final_day { get; set; }
}
{% endhighlight %}

The next request is to (http://www.bloomberg.com/billionaires/db/stats/2013/06/13) (notice the Date part of the URL - making a request for a specific
date will give you the information relevant to that date). The response this time is a "pure" JSON object, with a list of people. For each person,
here's a list of the information we get:

- Hold: summary of the Public, Private and Cash holdings of the person
- Last and Last_Rel: The change in net worth since the last update (divide by 100 to get the change in million dollars and percentages respectively)
- YTD and YTD_Rel: The change in net worth in the last year (divide by 100 to get the change in million dollars and percentages respectively)
- Net: Net worth of the person
- Rank: Ranking of the person in the list of Top Billionaires

We can deserialize the result into with the following code:

{% highlight csharp %}
public class Stats
{
    public int rank { get; set; }
    public int net { get; set; }
    public List hold { get; set; }
    public int last { get; set; }
    public int last_rel { get; set; }
    public int ytd { get; set; }
    public int ytd_rel { get; set; }
}
var people = await JsonConvert.DeserializeObjectAsync<IDictionary<string, Stats><idictionary<string, stats="">>(result);
{% endhighlight %}

This is a good start - we now have a list of the Top 100 Billionaires, and some general information about each of them, including their ranking and Net worth.

(**Note:** the original list of billionaires we got from the first request contains people that are not currently in the Top 100 list.
These people might not have their Stats available for the current date. If you want to retrieve their details, use the `final_day` field to
locate the last appearance of the person in the Top 100)

In the next part, we'll see how to retrieve the images of the people in the list.
