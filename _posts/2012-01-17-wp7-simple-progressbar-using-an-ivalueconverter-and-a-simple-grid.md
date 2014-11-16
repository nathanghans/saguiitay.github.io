---
layout: post
title: "WP7 simple ProgressBar - using an IValueConverter and a simple Grid"
date: 2012-01-17 14:07
author: saguiitay
comments: true
categories: [Development, Windows Phone]
tags: [.Net, Windows Phone, XAML, IValueConverter]
share: true
redirect_from:
 - /2012-01-17-wp7-simple-progressbar-using-an-ivalueconverter-and-a-simple-grid/
 - /development/wp7-simple-progressbar-using-an-ivalueconverter-and-a-simple-grid/
---
Creating a custom ProgressBar, using a Grid and an IValueConverter, is extremely easy, and leaves you with a bit more control than the built-in ProgressBar. 

Let's start by writing the Grid's [XAML](http://en.wikipedia.org/wiki/Extensible_Application_Markup_Language "Extensible Application Markup Language"):

{% highlight xml %}
<Border BorderBrush="Silver" BorderThickness="1" HorizontalAlignment="Stretch" Margin="0" Name="border1" VerticalAlignment="Top" Height="25">
    <Grid>
		<Grid.ColumnDefinitions>
			<ColumnDefinition Width="{Binding Percentage, Converter={StaticResource DoubleToGridLengthStarConverter}}"/>
			<ColumnDefinition Width="{Binding Percentage, Converter={StaticResource DoubleToInvertedGridLengthStarConverter}}"/>
		</Grid.ColumnDefinitions>
		
		<Rectangle HorizontalAlignment="Stretch" Grid.Column="0" Margin="0" Fill="{StaticResource PhoneAccentBrush}" StrokeThickness="1" VerticalAlignment="Stretch"/>
	</Grid>
</Border> 
{% endhighlight %}

The code above creates a Grid (with a border), and adds 2 columns to the grid. The colomns' width is bound to the DataContext's Percentage property, 
and converted using the value-converters we'll create shorty. We then add a rectangle shape (which will be placed in the first column).

Now, on to the value-converters. 

For simplicity, and since it might be useful later on, I've created 2 converters - the first will handle the widths of the "filled" part of the ProgressBar.
The second is used for the remaining "empty" area. The converters are simple - they take a percentage value (in the 0.0 - 1.0 range), 
and convert them to a GridLength, which will control the width of our columns: 

{% highlight csharp %}
public class DoubleToGridLengthStarConverter : IValueConverter 
{
	public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
	{
		if (value == null)
			return null;
		
		var doubleValue = (double) value;
		
		return new GridLength(doubleValue, GridUnitType.Star);
	}
	
	public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
	{
		throw new NotImplementedException();
	}
}

public class DoubleToInvertedGridLengthStarConverter : IValueConverter
{
	public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
	{
		if (value == null)
			return null;
		
		var doubleValue = (double)value;
		
		return new GridLength(1 - doubleValue, GridUnitType.Star);
	}
	
	public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
	{
		throw new NotImplementedException();
	}
}
{% endhighlight %}

We can then add the 2 converters to our resources: 

{% highlight xml %}
	<Converters:DoubleToGridLengthStarConverter x:Key="DoubleToGridLengthStarConverter" />
	<Converters:DoubleToInvertedGridLengthStarConverter x:Key="DoubleToInvertedGridLengthStarConverter" />
{% endhighlight %}

Here's a sample usage, from my upcoming application: 
![Custom ProgressBar]({{site.url}}/images/custom-progressbar.png "Custom ProgressBar")
