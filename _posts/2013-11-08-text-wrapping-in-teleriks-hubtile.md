---
title: "Text Wrapping in Telerik's HubTile"
date: 2013-11-08 17:45
author: saguiitay
categories: [Development, Windows Phone]
tags: [.Net, Telerik, Windows Phone]
redirect_from:
 - /2013/11/08/text-wrapping-in-teleriks-hubtile/
 - /2013-11-08-text-wrapping-in-teleriks-hubtile/
 - /development/text-wrapping-in-teleriks-hubtile/
---
[Telerik's HubTile](http://www.telerik.com/products/windows-phone/overview/all-controls/hubtile.aspx) control is quite useful when trying to create
native looking apps:
![Tiles1]({{site.url}}/images/tiles1.png)

The above image can be easily generate using the following XAML:

{% highlight xml %}
<ListBox>
	<ListBox.ItemsPanel>
		<ItemsPanelTemplate>
			<telerikPrimitives:RadWrapPanel />
		</ItemsPanelTemplate>
	</ListBox.ItemsPanel>
	<ListBox.Items>
		<ListBoxItem>
			<telerikPrimitives:RadHubTile Title="Tile1" />
		</ListBoxItem>
		<ListBoxItem>
			<telerikPrimitives:RadHubTile Title="Tile2" />
		</ListBoxItem>
	</ListBox.Items>
 </ListBox>
{% endhighlight %}

However, if you need to display a longer text in the tile, the "regular" HubTile control will not wrap the text, resulting in a
truncated text:
![Tiles2]({{site.url}}/images/tiles2.png)

In order to work with long texts, we need to use the CustomHubTile control. We'll start by creating our DataTemplate:

{% highlight xml %}
<DataTemplate x:Key="StaticTile">
	<telerikPrimitives:RadCustomHubTile>
		<telerikPrimitives:RadCustomHubTile.FrontContent>
			<Grid Background="{StaticResource PhoneAccentBrush}">
				<TextBlock VerticalAlignment="Bottom" Text="{Binding}" Margin="8" TextWrapping="Wrap"/>
			</Grid>
		<telerikPrimitives:RadCustomHubTile.FrontContent>
	<telerikPrimitives:RadCustomHubTile>
</DataTemplate>
{% endhighlight %}

We'll then change our ListBox to use the above template. We'll also use an ItemsSource binding to get the list of tiles:

{% highlight xml %}
<ListBox ItemTemplate="{StaticResource StaticTile}" ItemsSource="{Binding Items}">
	<ListBox.ItemsPanel>
		<ItemsPanelTemplate>
			<telerikPrimitives:RadWrapPanel />
		</ItemsPanelTemplate>
	</ListBox.ItemsPanel>
</ListBox>
{% endhighlight %}

For simplicity, we'll just add the list of tiles to the page class itself, and set the page as its own DataContext (**you DO NOT want to do that in production! This is done here only to keep things simple**):

{% highlight csharp %}
public MainPage()
{
	InitializeComponent();
	DataContext = this;
}
public IEnumerable<string> Items
{
	get
	{
		yield return "Tile with a very long title";
		yield return "Tile2";
	}
}
{% endhighlight %}

And there you have it - nicely wrapped tiles:

![Tiles3]({{site.url}}/images/tiles31.png)
