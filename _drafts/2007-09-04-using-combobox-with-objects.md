---
layout: post
title: "Using ComboBox with objects"
date: 2007-09-04 12:19
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, development, GUI]
---
<span style="font-family:arial;">In most of the cases where you need to use a ComboBox, you need it only so the user will be able to choose between a couple of string values. In the majority of the cases, you’ll have a list of objects that you’ll want the user to choose from, and you’ll have to do something similar to this: </span>

``` c#:html:nocontrols:nogutter
Hashtable idToItem = new Hashtable();
foreach (Item item in listOfItems)
{
    int index = comboBox.Add(item.DisplayName);
    idToItem[index] = item;
}
```

<span style="font-family:arial;">We can now use the SelectedIndex field of the ComboBox, in conjuction with the Hashtable to retrieve the selected item. While the above code works perfectly, it does consume more memory, and takes more time than needed. And while you might say that both the time and memory wasted are negligible, try to use 10,000 items to a ComboBox, that go enjoy a cup of coffee while you wait for things to move around… There exist a simple and elegant way of doing the same thing. The DisplayMember field of the ComboBox is mostly known in relation to binding the control to a data source. We are going to use it for something similar - our data sources will be the items themselves:</span>

``` c#:html:nocontrols:nogutter
comboBox.DisplayMember = "DisplayName";
foreach (Item item in listOfItems)
{
    comboBox.Add(item);
}
```

<span style="font-family:arial;">Now we can just use the SelectedItem field of the ComboBox, casting it to the correct type.</span>



