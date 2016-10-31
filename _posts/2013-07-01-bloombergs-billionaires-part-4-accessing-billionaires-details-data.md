---
title: "Bloomberg's Billionaires - Part 4: Accessing Billionaires' details data"
date: 2013-07-01 22:07
author: saguiitay
categories: [Development, Windows Phone]
tags: [.Net, Billionaires, Windows Phone]
redirect_from:
 - /2013-07-01-bloombergs-billionaires-part-4-accessing-billionaires-details-data/
 - /development/bloombergs-billionaires-part-4-accessing-billionaires-details-data/
---
In Part 2 we've learned how to retrieve the global information on the Top 100 Billionaires. In Part 3 we've seen how to get the images of the billionaires.
In this part we're going to learn how to retrieve detailed information on each billionaire. For each billionaire, we should already have an ID,
which we received in the global information (see Part 2). Using this ID, it's very simple to get the detailed information - let's see a request
for Bill Gates' information.

Making a simple web request to (http://www.bloomberg.com/billionaires/db/people/bill-gates) will give use a very nice JSON:

![]({{site.url}}/images/070113_2009_bloombergsb1.png)

As you can see, we have some Overview information (which contains a general description of the person, as well as some Intel facts),
a Portfolio (with a summary list of holdings, as well as detailed public & private holdings, cash and liabilities), some relevant News
items, some "interesting" ownerships (who knew that Gates has a 16<sup>th</sup> century Leonardo da Vinci work?!), and even some
personal biography (including textual bio and major milestones).

As usual, with the help of (http://json2csharp.com/) we can relatively easily create a C# object model: 

{% highlight csharp %}
public class Person
{
	public Overview overview { get; set; }
	public Confidence confidence { get; set; }
	public Portfolio portfolio { get; set; }
	public List<News> news { get; set; }
	public List<Own> owns { get; set; }
	public Worth worth { get; set; }
	public Bio bio { get; set; }
}

public class Bio
{
	public List<string> body { get; set; }
	public List<Milestone> milestones { get; set; }
	public Stats stats { get; set; }
}

public class News
{
	public string date { get; set; }
	public string link { get; set; }
	public string title { get; set; }
	public string thumb { get; set; }
}

//... more classes removed for clarity
{% endhighlight %}

And, using [Json.NET](http://james.newtonking.com/projects/json-net.aspx) we can easily convert our JSON to an our model:

{% highlight csharp %}
var personDetails = await JsonConvert.DeserializeObjectAsync<Person>(jsonString);
{% endhighlight %}

We can now proceed to work with the retrieved information - probably display it to the user in a nice view.
