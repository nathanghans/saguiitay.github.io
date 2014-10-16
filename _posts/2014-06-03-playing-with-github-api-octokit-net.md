---
layout: post
title: "Playing with GitHub API - Octokit.Net"
date: 2014-06-03 14:02
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, GitHub]
share: true
redirect_from:
 - /2014/06/03/playing-with-github-api-octokit-net/
 - /2014-06-03-playing-with-github-api-octokit-net/
 - /2014-06-03-playing-with-github-api-octokit-net
---
For a small project I'm working on, I needed access to [GitHub](http://www.github.com), to investigate some repositories via code. Luckily,
GitHub have an extremely extensive API. Furthermore, they even provide a .Net library (via a [NuGet package](http://www.nuget.org/packages/Octokit/))
named [Octokit.Net](https://github.com/octokit/octokit.net), to easily access that API:

{% highlight powershell %}
    Install-Package Octokit
{% endhighlight %}

Let's see some samples. We'll start by creating a new GitHubClient object:

{% highlight csharp %}
var github = new GitHubClient(new ProductHeaderValue("TestGitHutAPI"));
{% endhighlight %}

If you plan to use the API internally (using your own account, and not for your end-users), you can create a "Personal access token"
in your profile at <https://github.com/settings/applications>, and then use that token as follow:

{% highlight csharp %}
    github.Credentials = new Credentials("<Token goes here>");
{% endhighlight %}

Alternatively, you can register your application in GitHub, and use the details to authorize your end-users via a regular OAuth2 flow:

{% highlight csharp %}
var oauthLoginRequest = new OauthLoginRequest(clientId);
oauthLoginRequest.Scopes.Add("user");
oauthLoginRequest.Scopes.Add("public_repo");
var loginUrl = github.Oauth.GetGitHubLoginUrl(oauthLoginRequest);

// Redirect your users to the LoginUrl
// You will receive the"code"used below in the Redirect URI configured in your GitHub application

var oauthTokenRequest = new OauthTokenRequest(clientId,clientSecret,code);
var oauthToken = await github.Oauth.CreateAccessToken(oauthTokenRequest);
github.Credentials = new Credentials(oauthToken.AccessToken);
{% endhighlight %}

We are now ready to do something with the GitHubClient. Let's do a search for repositories:

{% highlight csharp %}
var searchRepositoriesRequest = new SearchRepositoriesRequest("nosql")
	{
        Language = Language.CSharp, 
		Fork = ForkQualifier.ExcludeForks, 
		Sort = RepoSearchSort.Stars, 
		Order = SortDirection.Descending, 
		PerPage = 10
    };
 
SearchRepositoryResult searchRepositoryResult = await github.Search.SearchRepo(searchRepositoriesRequest);
{% endhighlight %}

This will search for repositories that have the term "NoSQL" in them, that use the C# language. We will get the first 10 repositories, based on a descending
number of Stars, and we'll not get any Forked repositories. This is quite straight forward. Let's see another sample, this one of searching for files
inside a given repository:

{% highlight csharp %}
var codeSearch = new SearchCodeRequest(".csproj")
	{
		Repo = repository.FullName, 
		In = new[] {CodeInQualifier.Path}
	};

var codeResult = await github.Search.SearchCode(codeSearch);
{% endhighlight %}

This will search for all files that have "CSPROJ" in their path (which will result in all the C# Project files) within the given
repository (based on the repository's FullName).

Lastly, let's see how we can retrieve the content of a file that we received in the above search. We'll need the Login of the repository's owner, the
repository name, and the file's "SHA" (which functions as a version/id for the purpose of this request):

{% highlight csharp %}
var blob = await github.GitDatabase.Blob.Get(repo.Owner.Login, repo.Name, packageFile.Sha);
var contentBase64 = blob.Content;
var content = Convert.FromBase64String(contentBase64);
{% endhighlight %}

The Octokit.Net package is very comprehensive, and provides access to the following information in GitHub:

- Activities
- Deployment
- Gist
- GitDatabase
- Issues
- Notifications
- Organizations
- Releases
- Repositories
- Search
- Users

You can find the GitHub API documentation here: <https://developer.github.com/v3/>
