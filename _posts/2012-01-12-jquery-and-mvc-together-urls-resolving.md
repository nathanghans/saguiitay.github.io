---
layout: post
title: "jQuery and MVC together - URLs resolving"
date: 2012-01-12 22:47
author: saguiitay
comments: true
categories: [Development]
tags: [JavaScript, JQuery, MVC]
share: true
redirect_from:
 - /2012/01/12/jquery-and-mvc-together-urls-resolving/
 - /2012-01-12-jquery-and-mvc-together-urls-resolving/
 - /2012-01-12-jquery-and-mvc-together-urls-resolving
---
Lately I've found myself working on a website, that is based on MVC3, and is using some [jQuery](http://jquery.com/ "JQuery"). 
I found a beautiful template in [ThemeForest.net](http://themeforest.net?ref=saguiitay) - no shame in admitting I'm no designer.

The first few steps of setting up a new MVC project, using the downloaded theme in the Layout.chtml file, and adding all the files
went pretty smoothly. However, after playing a bit with the new site, I've started getting errors in some of the javascript files.
It turned out that the theme I was using jQuery's "include" method in several places. When loading the default Index action, things went fine. 
However, when navigating to other actions, due to the URL including the Controller's and Action's name, the "include" method couldn't find the 
files due to their relative path. Obviously, you can't use MVC's "Url" helper class from within the JS files to
resolve a "[~/](http://en.wikipedia.org/wiki/Home_directory "Home directory")" URL. 

The workaround I've found is to create a JS prototype in my Layout.chtml file, and use it's method to resolve the URLs in JS. Here is the code:

{% highlight javascript %}
	Url = function () { }; 
	Url.prototype = { 
		// Resolve ~/ using the MVC view engine 
		_relativeRoot: '@Url.Content("~/")', 
		
		// Create an extension method called "resolve"
		resolve: function(relative){ 
			var resolved = relative; 
			if (relative.charAt(0) == '~' && relative.charAt(1) == '/') 
				resolved = this._relativeRoot + relative.substring(2);
			return resolved; 
		} 
	}; 
	
	$Url = new Url();
{% endhighlight %}

In all your JS, you can now use the "resolve" method like this: 

{% highlight javascript %} 
	$.include($Url.resolve('~/js/jquery.watermarkinput.js'));
{% endhighlight %}
