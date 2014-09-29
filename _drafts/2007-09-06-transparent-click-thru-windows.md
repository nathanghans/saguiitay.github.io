---
layout: post
title: "Transparent Click-thru windows"
date: 2007-09-06 12:14
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, development, GUI]
---
<span style="font-family:arial;">It is often usefull to provide the user with information on the screen, without interfering his work. Usually, you would just create a window with a somewhat lower opacity, allowing the user to see what’s beneath your window. However, this window will still receive all the normal windows events, and mostly, the user’s mouse clicks. To prevent this, all you have to do is override the window’s CreateParams accessor, and add to the ExStyle property the WS\_EX\_TRANSPARENT value. Let’s have a look: </span>

``` c#:html:nocontrols:nogutter
protected override CreateParams CreateParams
{
    get
    {
        CreateParams cp = base.CreateParams;
        cp.ExStyle = 0×00000020;
        return cp;
    }
}
```



