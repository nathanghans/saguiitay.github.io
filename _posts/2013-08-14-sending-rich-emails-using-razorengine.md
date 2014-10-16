---
layout: post
title: "Sending rich emails using RazorEngine"
date: 2013-08-14 23:24
author: saguiitay
comments: true
categories: [Development]
tags: [Email, MVC, RazorEngine]
share: true
redirect_from:
 - /2013/08/14/sending-rich-emails-using-razorengine/
---
RazorEngine is a templating engine based on Microsoft's Razor parsing engine. One of its most common uses it to generate HTML for emails.
This post shows how to use the RazorEngine in a simple and elegant fashion. Let's start with some basic code to create our MailMessage,
which we will expand and build throughout this post:

{% highlight csharp %}
var htmlTemplate = ""; 
// TODO: Load the template from a CSHTML file
var config = new TemplateServiceConfiguration();

using (var service = newTemplateService(config))
{
	resultHtml = service.Parse(htmlTemplate, model, null, null);
}

var mailMessage = new MailMessage
	{
		BodyEncoding = Encoding.UTF8,
		IsBodyHtml = true,
		Body = resultHtml
	};
{% endhighlight %}

This is our most basic code - it initializes a new RazorEngine TemplateService instance, parses the provided template with the provided model,
and create a new instance of MailMessage. Let's starting enhancing this code into something more useful.

# Adding support for AlternativeViews

If we want to support alternative views, in case the recipient is using a less advanced mail client, or just prefer plain text over rich text,
we can load 2 templates (one for RichText, and one for plain text), and parse both. While we're at it, we'll make the assignment of the body of
the message a bit smarter too - if we have only a RichText body, we'll use only that; if we have only a PlainText body, we'll us it; if we have both we'll use both:

{% highlight csharp %}
string resultHtml = null;
string resultText = null;

var htmlTemplate = ""; // TODO: Load template from a CSHTML file 
var textTemplate = ""; // TODO: Load template from a TXT file
var config = new TemplateServiceConfiguration(); 
using (var service = new TemplateService(config))
{
	if (!string.IsNullOrWhiteSpace(htmlTemplate))
		resultHtml = service.Parse(htmlTemplate, model, null, null);
	if (!string.IsNullOrWhiteSpace(textTemplate))
		resultText = service.Parse(textTemplate, model, null, null);
}
var mailMessage = new MailMessage
	{
		BodyEncoding = Encoding.UTF8
	};
if (string.IsNullOrEmpty(resultHtml))
{
	mailMessage.Body = resultText;
}
else
{
	mailMessage.IsBodyHtml = true;
	mailMessage.Body = resultHtml;
	if (!string.IsNullOrEmpty(resultText))
	{
		using (var contentStream = new MemoryStream(Encoding.UTF8.GetBytes(resultText)))
			mailMessage.AlternateViews.Add(new AlternateView(contentStream, "text/plain"));
	}
}
{% endhighlight %}

# Putting the logic in a nice abstract class

Putting the mailing logic in a nicely wrapped class allows us to hide it being an interface, as well as inherit from the class in order to
create simple "mailer" classes. This will result in a pattern very similar to the Controller in ASP.Net MVC:

{% highlight csharp %}
public abstract class MailerBase
{
	protected MailMessage GenerateMail<T>(T model)
	{
		// ... all the code from the above section
	}
	protected void EmailAsync(MailMessage mailMessage)
	{
		var smtpClient = new SmtpClient();
		smtpClient.SendCompleted += (sender, args) => 
			{
				if (args.Error != null)
				{
					// TODO: Do something with the error, such as logging it
				}
			};
		smtpClient.SendAsync(mailMessage, mailMessage);
	}
}
{% endhighlight %}

We can now create a class that inherit from MailerBase, and can call GenerateMail to create a nicely formatted MailMessage. 
But this is not very useful if the template is hardcoded. Let's improve our template loading logic.

# Locating the template like an MVC project

It make sense to keep a project structure like a regular ASP.NET MVC project, even if we're not doing any ASP.Net - the separation of the 
templates from the model and from the code that sends the messages just makes common sense. So let's add a method to locate our template for us:

{% highlight csharp %}
public virtual string ViewPath { get; set; } 

private string GetTemplate(string viewName, string extension = ".html")
{
	string filename;
	if (!string.IsNullOrEmpty(ViewPath))
	{
		filename = string.Format("Views/{0}/{1}{2}", ViewPath, viewName, extension);
		if (File.Exists(filename))
			return File.ReadAllText(filename);
	}
	var typeName = GetType().Name;
	filename = string.Format("Views/{0}/{1}{2}", typeName, viewName, extension);
	if (File.Exists(filename))
		return File.ReadAllText(filename);
	
	filename = string.Format("Views/{0}{1}", viewName, extension);
	if (File.Exists(filename))
		return File.ReadAllText(filename);
		
	return null;
}
{% endhighlight %}

We can now change our GenerateMail method to get the template/view name, and load it dynamically:

{% highlight csharp %}
protected MailMessage GenerateMail<T>(T model, string viewName = "")
{
	string resultHtml = null;
	string resultText = null;
	
	var htmlTemplate = GetTemplate(viewName);
	var textTemplate = GetTemplate(viewName, ".txt");
	// ...rest of the method goes here...
}
{% endhighlight %}

# Inheriting from MailerBase

We can now start to use MailerBase in a meaningful way. Let's create a sample mailer class:

{% highlight csharp %}
public class UserMailer : MailerBase, IUserMailer
{
	public void PasswordReset(ResetViewModel viewModel)
	{
		var message = GenerateMail(viewModel, "PasswordReset");
		message.To.Add(viewModel.Email);
		message.Subject = "Your Password reset request";
		
		EmailAsync(message);
	}
}
{% endhighlight %}

Looking at this code, something looks fishy - the "PasswordReset" string bothers me. Let's remove the need to manually pass the template name
(unless the user wants to specify it explicitly). Let's change the signature of the GenerateMail method, and use the new 
`CallerMemberName` attribute:

{% highlight csharp %}
protected MailMessage GenerateMail<T>(T model, [CallerMemberName] string viewName = "") 
{% endhighlight %}

We can now call GenerateMail without specifying the view name, and the name of the calling method will be used automatically:

{% highlight csharp %}
var message = GenerateMail(viewModel);
{% endhighlight %}

# Summary

We now have a base class - MailerBase - which gives use the capability to easily send well formatted (using RazorEngine),
multi-view (via AlternativeView), dynamically loaded (using the GetTemplate method and some `CallerMemberName` magic) emails.
We've reached a state where creating new mailers is just as simple as creating ASP.Net MVC Controllers - you just inherit from MailerBase,
and implement some very simple logic to generate the email message and send it.

This should be enough for now. New time, we'll see how we can improve the HTML generated by RazorEngine to be even more end-user friendly.
