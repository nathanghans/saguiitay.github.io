---
layout: post
title: "Modeling GitHub Repositories and NuGet Packages"
date: 2014-06-06 14:44
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, GitHub, NuGet]
share: true
redirect_from:
 - /2014-06-06-modeling-github-repositories-and-nuget-packages/
 - /development/modeling-github-repositories-and-nuget-packages/
---
I'm planning to do some research and statistics on [NuGet](http://www.nuget.org) packages usage in [GitHub](http://www.github.com) repositories.
Here's some sample questions I would like to answer:

- Which NuGet packages are **really** popular (real usage vs. just download numbers)?
- Which NuGet packages are most popular for specific areas (logging, testing, etc.)?
- Which repositories are using specific NuGet packages?

I've shown in my previous post how to [retrieve the list of packages used by 
specific GitHub repository]({% post_url 2014-06-04-getting-all-nuget-packages-used-by-a-github-repository %}). And in a different post,
I've shown how to [retrieve the information of a NuGet package]({% post_url 2014-06-05-getting-nuget-package-details %}). We can use all 
that to collect all the information we need to answer the above questions. I'll start by creating my model:

{% highlight csharp %}
public class Repo
{
	public string Id { get { return "Repositories/" + RepoId; } }
	public string RepoId { get; set; }
	public string Name { get; set; }
	public string FullName { get; set; }
	public DateTime Updated { get; set; }
 
	public int ForksCount { get; set; }
	public int WatchersCount { get; set; }
 
	public IEnumerable<PackageRef> Packages { get; set; }
}

public class Package
{
	public string Id { get { return "Packages/" + Name + "/" + Version; } }
	public string Name { get; set; }
	public string Version { get; set; }
           
	public string Summary { get;  set; }
	public DateTime? Updated { get; set; }
	public IEnumerable<string> Tags { get; set; }
}

public class PackageRef
{
	public string Id { get "Packages/" + Name + "/" + Version; } }
	public string Name { get; set; }
	public string Version { get; set; }
}
{% endhighlight %}

As you can see, we have a simple Repository class, with its Name, Full-Name, a bit of metadata, and a list of references to Packages.
We also have a Package model, which includes the Name, Version, Tags and some additional fields. If you're asking yourself why I've modeled
things this way, it is because I'll be using [RavenDb](http://ravendb.net/) as my storage, and this follows
[RavenDb's recommendation/conventions](http://ravendb.net/docs/2.5/theory/document-structure-design) for document Ids, relationships and (de)normalization.

After this, it's just a matter of writing a "crawler". I'll show only the pseudo code here, since it's nothing groundbreaking:

- Search all C# repositories in GitHub
- For each repository:
  - Get list of packages
  - Save repository in the database
- For each collected package:
  - Get package details from NuGet
  - Save package in the database

There are of course a few challenges worth mentioning:

1. The GitHub API returns paged results, so we need to make sure we iterate over the repositories in pages (<https://developer.github.com/v3/#pagination>)
2. The GitHub API have a rate-limit, so we need to throttle our requests (<https://developer.github.com/v3/#rate-limiting>)
3. We are working in a big-data world - we'll have to work with "chunks" of data, and save/load things from our database as we go along. This means that we also need to be careful not to do unnecessary work, by checking the database if we've already have all the relevant information instead of re-querying the (GitHub, NuGet) services.
