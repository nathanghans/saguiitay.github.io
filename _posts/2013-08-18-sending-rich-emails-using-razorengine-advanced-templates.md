---
title: "Sending rich emails using RazorEngine - advanced templates"
date: 2013-08-18 13:08
author: saguiitay
categories: [Development]
tags: [C#, Email, MVC, RazorEngine]
redirect_from:
 - /2013-08-18-sending-rich-emails-using-razorengine-advanced-templates/
 - /development/sending-rich-emails-using-razorengine-advanced-templates/
---
In my previous post, I've shown how to use the RazorEngine template engine in order to generate EmailMessages in an MVC-like pattern.
However, when compared to "real" MVC, we have the ability to create more complex views, using utility classes/methods, such as @Html and @Url.
In this article we'll see how we can simulate that behavior, by using a custom Template base class. For this article, we're going to show how to
embed a CSS file into the template, thus allowing us to generate more user-friendly email.

Let's start with our code from the previous post, and see how we can improve it:

{% highlight csharp %}
protected MailMessage GenerateMail<T>(T model, [CallerMemberName] string viewName = "")
{
	string resultHtml = null;
	string resultText = null;
	
	var htmlTemplate = GetTemplate(viewName);
	var textTemplate = GetTemplate(viewName, ".txt");
	
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
	if (string.IsNullOrEmpty(resultText) && string.IsNullOrEmpty(resultHtml))
		throw new InvalidOperationException("View was not found");
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
	return mailMessage;
}
{% endhighlight %}

The first thing we want to do, is to tell RazorEngine to use our own custom Template class:

{% highlight csharp %}
var config = new TemplateServiceConfiguration { BaseTemplateType = typeof(MvcTemplateBase<T>) };
{% endhighlight %}

Now let's go ahead and define the MvcTemplateBase class:

{% highlight csharp %}
///<summary>
/// Provides a base implementation of an MVC-compatible template.
///</summary>
public abstract class MvcTemplateBase<T> : TemplateBase<T>
{
	public IEncodedString EmbedCss(string path)
	{
		// load the contents of that file
		string cssText;
		try
		{
			cssText = File.ReadAllText(path);
		}
		catch (Exception)
		{
			// blank string if we can't read the file for any reason
			cssText = "";
		}
		return Raw(cssText);
	}
}
{% endhighlight %}

Our class is quite simple - it's an abstract class that inherits from TemplateBase, and adds any utility method we need. Instead of a utility method,
you can easily add a property to a utility class, such as Html, or Url. In our case, we've added a method that loads the content of a file, and output
it as raw data. Finally, let's look at how our new template look like:

{% highlight aspx-cs %}
@inherits Namespace.MvcTemplateBase<Namespace.ResetViewModel>
<!DOCTYPE html>
<html lang="en">
<head>
	<title>Custom Template sample</title>
	@EmbedCss("Content/bootstrap.min.css")
</head>
<body>
	<div class="container">
		<div class="hero-unit">
			<h1>Reset Password Request</h1>
			<p>You requested to reset your password. Please click the following link to start the password reset process:</p>
			<p>
				<a class="btn btn-primary btn-large" href="@Model.Url">Reset Password</a>
			</p>
		</div>
	</div>
</body>
</html>
{% endhighlight %}

As you can see, we have a simple html. Just like in ASP.Net, we use the `@inherit` keyword to declare the view inherits from MvcTemplateBase,
and uses the ResetViewModel as our model. In the view itself, we can use the EmbedCss utility method by calling @EmbedCss('*file path*').
We can also use out model, by calling @Model - just like in ASP.Net MVC.
