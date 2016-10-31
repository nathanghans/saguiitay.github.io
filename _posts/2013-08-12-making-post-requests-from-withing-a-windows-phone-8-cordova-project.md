---
title: "Making POST requests from withing a Windows Phone 8 Cordova project"
date: 2013-08-12 15:16
author: saguiitay
categories: [Development, Windows Phone]
tags: [Cordova, JavaScript, PhoneGap, Windows Phone]
redirect_from:
 - /2013-08-12-making-post-requests-from-withing-a-windows-phone-8-cordova-project/
 - /development/making-post-requests-from-withing-a-windows-phone-8-cordova-project/
---
So you've created your Cordova/PhoneGap application, and now you need to make a POST request to a service on the web. 
You'll quickly find that this is not easily done. Here's a simple, extendable solution for that, using Cordova's Commands mechanism:

First, we'll create a new Command:

{% highlight csharp %}
public class PhonegapWindowsPhonePost : BaseCommand
{
    [DataContract]
    public class PhonegapWindowsPhonePostObject
    {
        [DataMember(IsRequired = false, Name = "url")]
        public string url;
        [DataMember(IsRequired = false, Name = "type")]
        public string type;
        [DataMember(IsRequired = false, Name = "data")]
        public dynamic data;
    }
    
    public void post(string options)
    {
        var allArgs = Newtonsoft.Json.JsonConvert.DeserializeObject<List<string>>(options);
        
        var methodArgs = Newtonsoft.Json.JsonConvert.DeserializeObject<PhonegapWindowsPhonePostObject>(allArgs[0]);
        try
        {
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    var wc = new WebClient();
                    var uri = new Uri(methodArgs.url);
                    wc.Encoding = Encoding.UTF8;
                    wc.Headers[HttpRequestHeader.ContentType] = "application/x-www-form-urlencoded";
                    wc.UploadStringCompleted += WebClient_UploadStringCompleted;
                    wc.UploadStringAsync(uri, methodArgs.type, methodArgs.data);
                });
        
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
    private void WebClient_UploadStringCompleted(object sender, UploadStringCompletedEventArgs e)
    {
        try
        {
            if (e.Result != null)
                DispatchCommandResult(new PluginResult(PluginResult.Status.OK, e.Result));
            else
                DispatchCommandResult(new PluginResult(PluginResult.Status.ERROR, "Error 401"));
        }
        catch (Exception ex)
        {
            DispatchCommandResult(new PluginResult(PluginResult.Status.ERROR, ex.Message));
            // no http status code available
        }
    }
}
{% endhighlight %}


This will allow us to call the "post" command, and pass the URL, Method and data to the WebClient. Next, we'll add our command to the list of
plugins in the config.xml file, to make it usable in our JavaScript code:

{% highlight xml %}
<plugins>
	<plugin name="Device"/>
    // ...
	<plugin name="InAppBrowser"/>
	<plugin name="PhonegapWindowsPhonePost" />
</plugins>
{% endhighlight %}

All that remains now, if to invoke the command from JavaScript:

{% highlight javascript %}
// Define the settings object which will be provided to the command 
var settings = [{
		url: "<Provide web service URL here>",
		type: "POST",
		data: "<Pass any data you want here"
	}];

// Define the success callback method
var success = function(result) {
	// Do something with the result
};

// Invoke the "post" method on the PhonegapWindowsPhonePost command
cordova.exec(success, null, "PhonegapWindowsPhonePost", "post", settings);
{% endhighlight %}
