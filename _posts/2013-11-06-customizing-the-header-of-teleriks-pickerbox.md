---
layout: post
title: "Customizing the Header of Telerik's PickerBox"
date: 2013-11-06 20:26
author: saguiitay
comments: true
categories: [Development, Windows Phone]
tags: [.Net, Telerik, Windows Phone]
share: true
redirect_from:
 - /2013/11/06/customizing-the-header-of-teleriks-pickerbox/
---
[Telerik's PickerBox](http://www.telerik.com/products/windows-phone/overview/all-controls/pickerbox.aspx) is a useful control that allows
users to multi-select items. You can see it in action on Telerik's product page. Like many of Telerik's controls, it has a Header property,
which saves you the trouble of creating an additional TextBlock control, format it, arrange it, etc.

However, the PickerBox control has a different style than other controls: 
![PickerBox Header Default Style]({{site.url}}/images/pickerbox-header-default-style.png)

Luckily, customizing the Header is quite simple (once you find the way to do it - the documentation is quite lacking in that area).

Inside the XAML of your PickerBox, include the following section

{% highlight xml %}
<telerikPrimitives:RadPickerBox.HeaderStyle>
	<Style TargetType="ContentControl">
		<Setter Property="HorizontalAlignment" Value="Left" />
		<Setter Property="Margin" Value="12,0" />
		<Setter Property="Foreground" Value="{StaticResource PhoneSubtleBrush}" />
	</Style>
<telerikPrimitives:RadPickerBox.HeaderStyle>
{% endhighlight %}

This will set the style of the Header to be left aligned, to have a 12 pixels margin, and to use the PhoneSubtleBrush brush
(which seems to be the same brush used by other headers). This will result in the following:
![PickerBox Header Customized Style]({{site.url}}/images/pickerbox-header-customized-style.png)
