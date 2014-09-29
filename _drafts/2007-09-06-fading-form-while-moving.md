---
layout: post
title: "Fading form while moving"
date: 2007-09-06 09:14
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, development, GUI]
---
<span style="font-family:arial;">The different between a nice application and an exellent one, is many time only in the small details. How much did the developers really put into it? How much they thought about every detail? The following code is nothing more than an eye-candy, but we all know that it’s those simple things, that take seconds to write that make the difference. Let’s write 2 methods to increase and decrease the opacity of a window:</span>

``` c#:html:nocontrols:nogutter
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
```

<span style="font-family:arial;">Now, we want the form do fade out when the user moves it, and fade back in when he releases the form. For this, we’ll catch the <span style="font-family:courier new;">WM\_ENTERSIZEMOVE</span> and <span style="font-family:courier new;">WM\_EXITSIZEMOVE</span> messages of the window, and call the above methods in a different thread:</span>

``` c#:html:nocontrols:nogutter
protected override void WndProc(ref Message m)
{
    switch(m.Msg)
    {
        case (int)0×231: //WM_ENTERSIZEMOVE
            Thread t = new Thread(new ThreadStart(DecreaseOpacity));
            t.Start();
            break;
        case (int)0×232: //WM_EXITSIZEMOVE
            Thread t2 = new Thread(new ThreadStart(IncreaseOpacity));
            t2.Start();
            break;
    }
    base.WndProc(ref m);
}
```



