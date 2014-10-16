---
layout: post
title: "Windsor: Loading installers from the execution folder"
date: 2011-09-10 13:52
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Windsor]
share: true
redirect_from:
 - /2011/09/10/windsor-loading-installers-from-the-execution-folder/
---
When using Windsor, it is necessary to load the various IWindowsInstallers into the container. One standard way of doing this is using a code similar to the below:

{% highlight csharp %}
container.Install(FromAssembly.InDirectory(new AssemblyFilter(".")));
{% endhighlight %}

The above code will load all the Installers that are located in the execution folder.

However, in certain cases, when your code is running in different contexts, such as under IIS, you need to be a bit more explicit about things.
You need to tell Windsor to load assemblies from the folder where you are running, and not from some run-time "copy" or cache folder.

For that, you can use the following class:

{% highlight csharp %}
public class ExecutingAssemblyFilter : AssemblyFilter
{
    public ExecutingAssemblyFilter()
        : base(new FileInfo(new Uri(Assembly.GetExecutingAssembly).CodeBase).LocalPath).Directory.ToString())
    { }
 }
{% endhighlight %}

This class inherits from `AssemblyFilter`, and automatically provides the correct path. It should be used like this:

{% highlight csharp %}
container.Install(FromAssembly.InDirectory(new ExecutingAssemblyFilter()));
{% endhighlight %}
