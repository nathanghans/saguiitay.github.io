---
layout: post
title: "DebbugerDisplayAttribute"
date: 2006-12-21 15:58
author: saguiitay
comments: true
categories: [Development]
tags: [.Net]
share: true
redirect_from:
 - /2006/12/21/debbugerdisplayattribute/
---
A little late, I know, but while watching the Channel 9: Scott Nonnenberg - Visualizers in VS 2005 webcast, I've learned the beauty of the DebuggerDisplayAttribute. For those of you not familiar with this attribute, it allow you to choose how the tooltips inside the IDE while debugging will look like, and what values they will show you. Look at the following example:

{% highlight csharp %}
[System.Diagnostics.DebuggerDisplay("Name={Name} ({Members.Count} members)")]
public class Group
{
    public string Name;
    public ArrayList Members = new ArrayList();
}

Group g = new Group();
g.Name = "Administrators";
g.Members.Add("User1");
g.Members.Add("User2");
g.Members.Add("User3");
{% endhighlight %}

If you run your application, and have an instance of a Group, here's how it will look like when you'll place the mouse ove the instance: Name="Aministrator" (3 members); This is very usefull if you have instances of complex objects, and you'd like to shorten the time you spend understanding that's the data that you are currently debugging.



