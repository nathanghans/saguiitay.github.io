---
layout: post
title: "Fading form while moving"
date: 2007-09-06 09:14
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, GUI]
share: true
redirect_from:
 - /2007/09/06/fading-form-while-moving/
---
The different between a nice application and an exellent one, is many time only in the small details. 
How much did the developers really put into it? How much they thought about every detail? 

The following code is nothing more than an eye-candy, but we all know that it's those simple things, 
that take seconds to write that make the difference.

Let's write 2 methods to increase and decrease the opacity of a window:

{% highlight csharp %}
private void DecreaseOpacity()
{
    while (this.Opacity > 0.2)
    {
        this.Opacity -= 0.02;
        double lastOpacity = this.Opacity;
        Thread.Sleep(10);
        if (this.Opacity != lastOpacity)
            break;
    }
}
private void IncreaseOpacity()
{
    while (1 > this.Opacity)
    {
        this.Opacity += 0.04;
        Thread.Sleep(10);
    }
}
{% endhighlight %}

Now, we want the form do fade out when the user moves it, and fade back in when he releases the form.
For this, we'll catch the `WM\_ENTERSIZEMOVE` and `WM\_EXITSIZEMOVE` messages of the window, and call 
the above methods in a different thread:

{% highlight csharp %}
protected override void WndProc(ref Message m)
{
    switch(m.Msg)
    {
        case (int)0x231: //WM_ENTERSIZEMOVE
            Thread t = new Thread(new ThreadStart(DecreaseOpacity));
            t.Start();
            break;
        case (int)0x232: //WM_EXITSIZEMOVE
            Thread t2 = new Thread(new ThreadStart(IncreaseOpacity));
            t2.Start();
            break;
    }
    base.WndProc(ref m);
}
{% endhighlight %}


