---
layout: post
title: "Prevent changing the state of a CheckBox"
date: 2007-09-05 12:18
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, development, GUI]
---
<span style="font-family:arial;">Preventing an item in a CheckedListBox or a ListView with CheckBoxed is a really basic requirement when dealing with GUI. The simple and elegant code is for some reason, quite under-sampled. Assuming that you know which item you want to prevent the user from changing, all you have to do is register on the ItemCheck event off either the CheckedListBox or the ListView, and write the following event handler: </span>

``` c#:html:nocontrols:nogutter
private void checkedListBox1_ItemCheck(object sender, System.Windows.Forms.ItemCheckEventArgs e)
{
    if (e.Index == 0)
        e.NewValue = e.CurrentValue;
}
```

<span style="font-family:arial;">One could add grayed-out coloring to the item, to visually notify the user that the item is disable, and of course, obviously you could keep a list of all indexes that are to be disabled this way. If you take a look at my previous post, and expand it to a CheckedListBox, you can just as easily keep the Enabled state of the item in the obect itself, and use that in the ItemCheck event.</span>



