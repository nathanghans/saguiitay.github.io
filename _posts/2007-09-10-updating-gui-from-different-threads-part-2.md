---
title: "Updating GUI from different threads - Part 2"
date: 2007-09-10 09:08
author: saguiitay
categories: [Development]
tags: [.Net, GUI]
redirect_from:
 - /2007-09-10-updating-gui-from-different-threads-part-2/
 - /development/updating-gui-from-different-threads-part-2/
---
As part of my work at [Tzunami Inc.](http://www.tzunami.com/), I'm working on migrating our product Deployer - 
a [SharePoint migration](http://www.tzunami.com/products) tool - from .Net 1.1 to .Net 2.0 and Visual Studio 2005. 

One of the first things we've encountered was the managed debugging assistants (MDA). 
I'll talk about the MDAs in a later entry, but for now, let's just say that those are exceptions that are thrown 
only in debug mode, and that assist you in finding bugs and problematic points in your code that are otherwise 
hard to locate. 

The first MDA that we've encountered was the "Cross-thread operation not valid". What this means, 
is that you're trying to update the GUI from a thread other than the thread that created the GUI control. 
You can read about how to solve this issue in .Net 1.1 in one of my previous entries. 

However, .Net 2.0 allows you to resolve this in a much cleaner way. Bellow is the way we decided to handle this:

{% highlight csharp %}
private void InvokeGuiDelegate(GUIDelegate d)
{
    if (InvokeRequired)
        BeginInvoke(d);
    else
        d();
}
{% endhighlight %}

Now we can use the above method everywhere we need to update the GUI, using an annonymous method:

{% highlight csharp %}
InvokeGuiDelegate( delegate() { numericUDTimeout.Value = value; });
{% endhighlight %}



