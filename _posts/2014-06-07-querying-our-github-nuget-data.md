---
layout: post
title: "Querying our GitHub & NuGet data"
date: 2014-06-07 16:27
author: saguiitay
comments: true
categories: [Development]
tags: [GitHub, NuGet, RavenDb]
share: true
redirect_from:
 - /2014/06/07/querying-our-github-nuget-data/
 - /2014-06-07-querying-our-github-nuget-data/
 - /2014-06-07-querying-our-github-nuget-data
---
In my previous posts, I've discussed how to [retrieve the list of top repositories from GitHub]({% post_url 2014-06-03-playing-with-github-api-octokit-net %}),
get the [list of packages each one uses]({% post_url 2014-06-04-getting-all-nuget-packages-used-by-a-github-repository %}), and [store all that information
in a RavenDb database]({% post_url 2014-06-06-modeling-github-repositories-and-nuget-packages %}). Now, it's time to query all the data.

RavenDb provides Map/Reduce indexes for querying the data. Let's see some of the basic ones that interest us.

## Repositories/ByName

{% highlight csharp %}
from repo in docs.Repos
select new {
	Name = repo.Name,
	FullName = repo.FullName
}
{% endhighlight %}

## Packages/ByName

{% highlight csharp %}
from package in docs.Packages
select new {
	Name = package.Name
}
{% endhighlight %}

## Packages/ByTag

{% highlight csharp %}
from package in docs.Packages
from tag in package.Tags
select new {
	Tag = tag
}
{% endhighlight %}

These are all simple indexes, which are there mostly for searching and viewing our data. Much more interesting is to see the relations between repositories
and packages. Let's look at an index that allows us to get a list of repositories that use a certain package.

Here's the index definition:

## Repositories/ByPackage

{% highlight csharp %}
from repo in docs.Repos
from package in repo.Packages
from tag in package.Tags
select new {
	PackageName = package.Name,
	PackageVersion = package.Version
}
{% endhighlight %}

Querying this index gives us a nice list of repositories: 

![GitHub-NuGet-Index1]({{site.url}}/images/github-nuget-index1.png)

We can also create a more complex index, that will tell us which package (regardless of its versions) is the most popularly used:

### MAP

{% highlight csharp %}
from repo in docs.Repos
from packageRef in repo.Packages
let package = LoadDocument(packageRef.Id)
select new {
	PackageName = packageRef.Name,
	Repos = new string[] { repo.FullName },
	Tags = package.Tags,
	Count = 1
}
{% endhighlight %}

### REDUCE

{% highlight csharp %}
from r in results
group r by r.PackageName into g
select new {
	PackageName = g.Key,
	Repos = g.SelectMany(x => x.Repos).Distinct(),
	Tags = g.SelectMany(x => x.Tags).Distinct(),
	Count = g.Sum(x => x.Count)
}
{% endhighlight %}

### FIELDS

- Tags:
  - Storage: Yes
  - Indexing: Analyze
- Count:
  - Sort: Int

This index allows us to see which packages are the most widely used: 

![GitHub-NuGet-Index2]({{site.url}}/images/github-nuget-index2.png)

And even drill further into the data, and for example, see which of the packages that have the "sql" tag is most widely used (and by which repositories): 

![GitHub-NuGet-Index3]({{site.url}}/images/github-nuget-index3.png)

Here I've shown you how to query the data. In my next post, I'll try to gain some insights from the data, and see if I can see some usage patterns.
