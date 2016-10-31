---
title: "Copy Rest API client in C#"
date: 2015-04-18 16:00
author: saguiitay
categories: [Development]
tags: [.Net, Copy]
redirect_from:
 - /2015-04-18-copy-rest-API-client-in-c/
 - /development/copy-rest-API-client-in-c/
---
Following my work creating [OneDrive Rest API](https://github.com/saguiitay/OneDriveRestAPI), [DropboxRestAPI](https://github.com/saguiitay/DropboxRestAPI)
and [WebDAVClient](https://github.com/saguiitay/WebDAVClient), I've decided to release my library for communicating with the [Copy](http://www.copy.com) cloud
storage service.

Basing myself on [abdurrachman-habibi](https://github.com/abdurrachman-habibi)'s work, I took his [Copy-.Net-SDK](https://github.com/abdurrachman-habibi/Copy-.Net-SDK)
library and modified it based on my needs. My changes included:

  * Improved async/await support
  * Better error handling
  * Fully support the Copy REST API, as [documented](https://developers.copy.com/documentation)
  
The source code is available here <https://github.com/saguiitay/CopyRestAPI> and the NuGetPackage here <https://www.nuget.org/packages/CopyRestAPI>

Here's the sample code taken from the GitHub repository:

{% highlight csharp %}
var client = new CopyClient(
    new Config
        {
            CallbackUrl = "https://www.yourapp.com/Copy",
            ConsumerKey = ConsumerKey,
            ConsumerSecret = ConsumerSecret,

            //Token = Token,
            //TokenSecret = TokenSecret
        });

// Perform authorization (alternatively, provide a Token and TokenSecret in the Config object passed to CopyClient
await Authorize(client);

// Retrieve information on the logged-in user
var user = await client.UserManager.GetUserAsync();

// Retrieve the root folder of the logged-in user
var rootFolder = await client.GetRootFolder();

foreach (var child in rootFolder.Children)
{
    var childInfo = await client.FileSystemManager.GetFileSystemInformationAsync(child.Id);
}
{% endhighlight %}

I hope this library helps someone - I'd love to get feedback and pull requests.
