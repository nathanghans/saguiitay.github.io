---
layout: post
title: "WP7: Sending yourself error reports"
date: 2012-01-04 01:21
author: saguiitay
comments: true
categories: [Development, Windows Phone]
tags: [Windows Phone]
share: true
redirect_from:
 - /2012/01/04/wp7-sending-yourself-error-reports/
 - /2012-01-04-wp7-sending-yourself-error-reports/
 - /development/wp7-sending-yourself-error-reports/
---
One useful way of getting feedback from your WP7 application, is to be notified whenever a user encounter a crash.
One easy way to achieve this, is to ask your users to send you an "error report" whenever the application crashes.
We'll start by adding the following code to the constructor of the App class: 

{% highlight csharp %}
// Global handler for uncaught exceptions.
UnhandledException += OnUnhandledException;
{% endhighlight %}

And in the OnUnhandledException method, we'll ask the user permission to send the error report, and then use the EmailComposeTask task:

{% highlight csharp %}
var result = MessageBox.Show("Would you like to email the details of this error to the Developer?\n\n"
	+ "(No personal information, other than your email address, will be sent to the Developer)", 
	"An error has occurred!", 
	MessageBoxButton.OKCancel);

if (result == MessageBoxResult.OK) 
{
	var task = new EmailComposeTask 
		{
			To = "your@hotmail.com",
			Subject = "Error Report: Your App Title",
			Body = "An unhandled exception occurred in App:\n\n" + "Version: 1.0.0.1\n\n" + e.ExceptionObject
		};
	task.Show();
}
{% endhighlight %}

This will allow you to receive an email with a few details of the error.
