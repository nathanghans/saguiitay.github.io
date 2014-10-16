---
layout: post
title: "WebDAV client in C#"
date: 2014-10-14 20:58
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, WebDAV]
share: true
redirect_from:
 - /2014/10/14/webdav-client-in-c/
---
I always prefer using existing libraries implemented by someone else - it means that I don't have to study the subject matter, and not less important, that someone
else maintains the code for me (test the code, implement new features, follow-up on newer versions of the APIs if any, etc.). 

That being said, every now and then I find myself searching for such solutions (whether it's in GitHub, CodePlex, Google Code, or perhaps just a blog post), and not
finding anything useful, even for basic requirements. Case in point - WebDAV client implemented in C#. Async/Await support are not required but preferred.

I've found several libraries which provide WebDAV support, the most interesting one being <https://github.com/kvdb/WebDAVClient>. But that library doesn't support
`async/await` out of the box, and in not strong-typed.

I ended up writing my own library, based on [KVDB](https://github.com/kvdb/)'s repository, with the following features:

* Available as a [NuGet packages](https://www.nuget.org/packages/WebDAVClient/)
* Fully support async/await
* Strong-typed, which should allow extension in the future
* Implemented using HttpClient, which means support for extendibility such as [throttling]({% post_url 2014-05-05-throttling-httpclient-requests %}) and 
[monitoring]({% post_url 2014-09-30-byte-rot-performance-counters-for-your-httpclient%})

Here's the sample code taken from the GitHub repository:

{% highlight csharp %}
// Basic authentication required
var client = new WebDAVClient.Client(new NetworkCredential { UserName = "USERNAME" , Password = "PASSWORD"});
// OR without authentication
var client = new WebDAVClient.Client(new NetworkCredential());

// Set basic information for WebDAV provider
client.Server = "https://webdav.4shared.com";
client.BasePath = "/";

// Retrieve list of items in root folder
var files = await client.List();

// Find folder named 'Test'
var folder = files.FirstOrDefault(f => f.Href.EndsWith("/Test/"));

// Reload folder 'Test'
var folderReloaded = await client.Get(folder.Href);

// Retrieve list of items in 'Test' folder
var folderFiles = await client.List(folder.Href);

// Find first file in 'Test' folder
var folderFile = folderFiles.FirstOrDefault(f => f.IsCollection == false);
// Downlaod file into a Stream
var stream = await client.Download(folderFile.Href);

// Upload a file (with a random name) to 'Test' folder
var tempName = Path.GetRandomFileName();
var fileUploaded = await client.Upload(folder.Href, File.OpenRead(@"<PATH TO FILE>"), tempName);

// Create a folder (with a random name) in the 'Test' folder
tempName = Path.GetRandomFileName();
var folderCreated = await client.CreateDir(folder.Href, tempName);
{% endhighlight %}

I hope this library helps someone - I'd love to get feedback and pull requests.
