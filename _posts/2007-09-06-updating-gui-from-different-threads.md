---
title: "Updating GUI from different threads"
date: 2007-09-06 12:16
author: saguiitay
categories: [Development]
tags: [.Net, GUI]
redirect_from:
 - /2007-09-06-updating-gui-from-different-threads/
 - /development/updating-gui-from-different-threads/
---
Even the smallest thing, when thought upfront, can help you go a long way. 
Whenever you create an application that is more that two forms thrown together, 
and that does a little bit more than just saying “Hello world”, you'll encounter a 
point where you'll want to use threads. 

So if you're going to reach that point somewhere along the way, why not plan and prepare for that in advance? 
Working with threads by itself is not the really topic of this post. 
However, changing the GUI from different threads is. 

Windows applications in .Net request that changes in Forms be made in the thread that owns the form. 
Managing that on your own can be a real pain. 

Thanksfully, the .Net team thought about us, and allowed us a simple way to handle this:

{% highlight csharp %}
public void UpdateForm()
{
    if (this.InvokeRequired())
    {
        this.Invoke(new MethodInvoker(UpdateForm));
        return;
    }
    // Update your form as usual here...
}
{% endhighlight %}

The more attentive readers will raise the point, that this example works only because UpdateForm() returns void and accepts no argument. 
Sadly this is true. For any other case we'll have to write a delegate, and send the `Invoke()` method an instance of that delegate, 
just as the following example shows:

{% highlight csharp %}
private delegate bool UpdateFormMethod(int argument);

public bool UpdateForm(int argument)
{
    if (this.InvokeRequired)
    {
        return (bool)this.Invoke(new UpdateFormMethod(UpdateForm),
            new object[] {argument});
    }
    // Update your form as usual here...
    return true;
}
{% endhighlight %}
