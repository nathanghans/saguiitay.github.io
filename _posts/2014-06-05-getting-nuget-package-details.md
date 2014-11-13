---
layout: post
title: "Getting NuGet package details"
date: 2014-06-05 15:07
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, NuGet, GitHub]
share: true
redirect_from:
 - /2014/06/05/getting-nuget-package-details/
 - /2014-06-05-getting-nuget-package-details/
 - /development/getting-nuget-package-details/
---
In my [previous post]({% post_url 2014-06-04-getting-all-nuget-packages-used-by-a-github-repository %}) I've shown how to get the list of
[NuGet](http://www.nuget.org) packages used by a [GitHub](http://www.github.com) repository. Let's see how we can retrieve some additional information
for each package. The NuGet Gallery provides an OData feed, which can be used to easily query and search its database. It's base URL
is <http://packages.nuget.org/v1/FeedService.svc>. I'll not go over the details and capabilities of an OData feed - you can read about
it [here](http://msdn.microsoft.com/en-us/library/ff478141.aspx). All I require are the details of a specific version of a specific package. So let's see the code:

{% highlight csharp %}
private void UpdatePackageDetails(Package package)
{
    var url = string.Format("http://packages.nuget.org/v1/FeedService.svc/Packages(Id='{0}',Version='{1}')",
	package.Name, package.Version);
 
    try
    {
        XDocument xdoc = XDocument.Load(url);
 
		var tags = xdoc.Descendants("{http://schemas.microsoft.com/ado/2007/08/dataservices}Tags").FirstOrDefault();
		if (tags != null)
		package.Tags = tags.Value.Split(new[] { ' ' }, StringSplitOptions.RemoveEmptyEntries);
 
		var summary = xdoc.Root.Element("{http://www.w3.org/2005/Atom}summary");
		if (summary != null)
			package.Summary = summary.Value;

		var updated = xdoc.Root.Element("{http://www.w3.org/2005/Atom}updated");
		if (updated != null)
			package.Updated = DateTime.Parse(updated.Value);
	}
    catch (WebException)
    { }
}
{% endhighlight %}

This code assumes that you have the Package class introduced in the [previous post]({% post_url 2014-06-04-getting-all-nuget-packages-used-by-a-github-repository %}),
with the Name and Version properties already populated. It's a very simple piece of code - it creates the URL for the Feed entry of the package/version
we need, loads it into an [XDocument](http://msdn.microsoft.com/en-us/library/system.xml.linq.xdocument(v=vs.110).aspx), and update the package instance
with the metadata we require - Tags, Summary, and Updated date.
