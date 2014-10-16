---
layout: post
title: "Filtering Win32 messages of a form"
date: 2007-09-06 12:12
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, GUI]
share: true
redirect_from:
 - /2007/09/06/filtering-win32-messages-of-a-form/
 - /2007-09-06-filtering-win32-messages-of-a-form/
 - /2007-09-06-filtering-win32-messages-of-a-form
---
I've recently came across a nice post on C# Frequently Asked Questions blog.
In the post, they've shown a way to filter out, or (pre)process Win32 messages of a form.

Let's have a look at the code, then we'll go through it.

{% highlight csharp %}
public class MyMessageFilter : IMessageFilter
{
    public bool PreFilterMessage(ref Message m)
    {
        // Intercept the left mouse button down message
        if (m.Msg == 513)
        {
            Console.WriteLine("WM_LBUTTONDOWN is: " + m.Msg);
            return true;
        }
        return false;
    }
}
{% endhighlight %}

And in the form itself:

{% highlight csharp %}
public class mainForm : System.Windows.Forms.Form
{
    private MyMessageFilter msgFliter = new MyMessageFilter();

    public mainForm()
    {
        // Register message filter
        Application.AddMessageFilter(msgFliter);
    }
}
{% endhighlight %}

As you can see, we've started by creating a class that implements the `IMessageFilter` interface. 
Then we've added an instance of that class to the form, and in the constructor registered it. 
We can use the `Application.RemoveMessageFilter()` method to stop filtering messages using the specified filter. 
All the filter class does is handle the `WM_LBUTTONDOWN` message. By returning true, we specify that we've 
handled the message, and there's no need of any further handling of it - meaning that no event will be sent.
This technique can be used either as a way to handle specific messages, or just ignore certain messages. 

Other nice things to try is to catch the message only a certain amount of times, or to "hide" some of the 
messages when certain requirements are met.
