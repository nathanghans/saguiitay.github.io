---
layout: post
title: "Querying our GitHub & NuGet data"
date: 2014-06-07 16:27
author: saguiitay
comments: true
categories: [.Net, Development]
tags: [github, nuget, RavenDb]
---
In my previous posts, I've discussed how to [retrieve the list of top repositories from GitHub](http://itaysagui.wordpress.com/2014/06/03/playing-with-github-api-octokit-net/), get the [list of packages each one uses](http://itaysagui.wordpress.com/2014/06/04/getting-all-nuget-packages-used-by-a-github-repository/), and [store all that information in a RavenDb database](http://itaysagui.wordpress.com/2014/06/06/modeling-github-repositories-and-nuget-packages/). Now, it's time to query all the data. RavenDb provides Map/Reduce indexes for querying the data. Let's see some of the basic ones that interest us. `    Repositories/ByName     from repo in docs.Repos     select new {         Name = repo.Name,         FullName = repo.FullName     }` `    Packages/ByName     from package in docs.Packages     select new {         Name = package.Name     }` `    Packages/ByTag     from package in docs.Packages     from tag in package.Tags     select new {         Tag = tag     }` These are all simple indexes, which are there mostly for searching and viewing our data. Much more interesting is to see the relations between repositories and packages. Let's look at an index that allows us to get a list of repositories that use a certain package. Here's the index definition: <span style="font-family:Courier New;">   ** <span style="text-decoration:underline;">Repositories/ByPackage</span>** <span style="font-family:Courier New;"><span style="color:blue;">    from</span> repo <span style="color:blue;">in</span> docs<span class="sc10">.</span>Repos</span> </span><span style="font-family:Courier New;"><span style="color:blue;">    from</span> package <span style="color:blue;">in</span> <span style="font-family:Courier New;">repo</span><span class="sc10">.</span>Packages <span style="color:blue;">    from</span> tag <span style="color:blue;">in</span> package<span class="sc10">.</span>Tags <span style="color:blue;">    select</span> <span style="color:blue;">new</span> <span class="sc10">{</span> <span style="color:blue;"><span style="font-family:Courier New;">    </span></span><span style="color:blue;"><span style="font-family:Courier New;">    </span></span>PackageName = package.Name, <span style="color:blue;"><span style="font-family:Courier New;">    </span></span><span style="color:blue;"><span style="font-family:Courier New;">    </span></span>PackageVersion = package.Version <span class="sc10"><span style="color:blue;"><span style="font-family:Courier New;">    </span></span>}</span></span> Querying this index gives us a nice list of repositories: [![GitHub-NuGet-Index1]({{site.url}}/images/github-nuget-index1.png)](https://itaysagui.files.wordpress.com/2014/06/github-nuget-index1.png) We can also create a more complex index, that will tell us which package (regardless of its versions) is the most popularly used:

-   MAP: `    from repo in docs.Repos     from packageRef in repo.Packages     let package = LoadDocument(packageRef.Id)     select new {         PackageName = packageRef.Name,         Repos = new string[] { repo.FullName },         Tags = package.Tags,         Count = 1     }`
-   REDUCE: `    from r in results     group r by r.PackageName into g     select new {         PackageName = g.Key,         Repos = g.SelectMany(x => x.Repos).Distinct(),         Tags = g.SelectMany(x => x.Tags).Distinct(),         Count = g.Sum(x => x.Count)     }`
-   FIELDS:
    -   Tags:
        -   Storage: Yes
        -   Indexing: Analyze
    -   Count:
        -   Sort: Int

This index allows us to see which packages are the most widely used: [![GitHub-NuGet-Index2]({{site.url}}/images/github-nuget-index2.png)](https://itaysagui.files.wordpress.com/2014/06/github-nuget-index2.png) And even drill further into the data, and for example, see which of the packages that have the "sql" tag is most widely used (and by which repositories): [![GitHub-NuGet-Index3]({{site.url}}/images/github-nuget-index3.png)](https://itaysagui.files.wordpress.com/2014/06/github-nuget-index3.png) Here I've shown you how to query the data. In my next post, I'll try to gain some insights from the data, and see if I can see some usage patterns.


