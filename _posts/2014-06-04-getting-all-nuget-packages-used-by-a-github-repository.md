---
layout: post
title: "Getting all NuGet packages used by a GitHub repository"
date: 2014-06-04 14:29
author: saguiitay
comments: true
categories: [.Net, Development]
tags: [.Net, development, github, nuget]
---
In my [previous post](http://itaysagui.wordpress.com/2014/06/03/playing-with-github-api-octokit-net/ "Playing with GitHub API – Octokit.Net"), I've shown how we can search for c\# repositories in [GitHub](http://www.github.com), how to search for files within a repository, and how to get the content of a file. We can now use these to ask a very specific question: *Which NuGet packages are used within a specific repository?* Let's start by defining a Package class, and a simple [IEqualityComparer](http://msdn.microsoft.com/en-us/library/ms132151(v=vs.110).aspx) for it (which will help us remove duplicate packages):

```
    public class Package
    {
        public string Id { get { return "Packages/" + Name + "/" + Version; } }
        public string Name { get; set; }
        public string Version { get; set; }
    }
```

```
    public class PackageComparer : IEqualityComparer<Package>
    {
        #region Implementation of IEqualityComparer<in Package>
 
        public bool Equals(Package x, Package y)
        {
            return string.Compare(x.Id, y.Id, StringComparison.InvariantCultureIgnoreCase) == 0;
        }
 
        public int GetHashCode(Package obj)
        {
            return obj.Id.GetHashCode();
        }
 
        #endregion
    }
```

Now, we'll get all the "*packages.config*" files within our repository:

```
    var codeSearch = new SearchCodeRequest("packages.config")
        {
            Repo = repo.FullName, 
            In = new[] {CodeInQualifier.Path}
        };
 
    var codeResult = await github.Search.SearchCode(codeSearch);
```

Now that we got the list of files, let's retrieve the content of each of them:

```
    var packagesFiles = codeResult.Items
        .Where(p => p.Name == "packages.config")
        .Select(packageFile => github.GitDatabase.Blob.Get(repo.Owner.Login, repo.Name, packageFile.Sha).Result)
        .Select(blob => blob.Content)
        .Select(Convert.FromBase64String)
```

All we need to do now, is parse each file, and get the list of packages from the resulting [XDocument](http://msdn.microsoft.com/en-us/library/system.xml.linq.xdocument(v=vs.110).aspx):

```
    private IEnumerable<Package> GetPackages(byte[] arg)
    {
        using (var memoryStream = new MemoryStream(arg))
        {
            var xdoc = XDocument.Load(memoryStream);

            var packages = xdoc.Descendants("package");
            foreach (var package in packages)
            {
                var id = package.Attribute("id").Value;
                var version = package.Attribute("version").Value;
 
                yield return new Package
                    {
                        Name = id,
                        Version = version
                    };
            }
        }
    }
```

Here's the final resulting method, which also includes removal of duplicate packages (remember, since we can have multiple projects within a Repository, we might get the same package several times):

```
    private async Task<IEnumerable<Package>> GetRepoPackage(GitHubClient github, Repository repository)
    {
        var codeSearch = new SearchCodeRequest("packages.config")
            {
                Repo = repository.FullName, 
                In = new[] {CodeInQualifier.Path}
            };
 
        var codeResult = await github.Search.SearchCode(codeSearch);
 
        var packages = codeResult.Items
            .Where(p => p.Name == "packages.config")
            .Select(packageFile => github.GitDatabase.Blob.Get(repo.Owner.Login, repo.Name, packageFile.Sha).Result)
            .Select(blob => blob.Content)
            .Select(Convert.FromBase64String)
            .SelectMany(GetPackages)
            .Distinct(new PackageRefComparer());

        return packages;
    }
```

