---
title: "Retrieving a list of available Documentum DocBases"
date: 2007-09-10 18:42
author: saguiitay
categories: [Development]
tags: [.Net, Documentum]
redirect_from:
 - /2007-09-10-retrieving-a-list-of-available-documentum-docbases/
 - /development/retrieving-a-list-of-available-documentum-docbases/
---
While adding support for EMC Documentum to the [Tzunami Deployer, our SharePoint migration tool](http://www.tzunami.net/), 
I needed to allow the user to enter the name of a DocBase to connect to. I wanted a interface that is a bit more that just 
a TextBox where the user can enter the DocBase name. 

I ended up using a ComboBox, and added a "Refresh" button, similar to the one used in the Server Explorer of Visual Studio. 
When the user press the "Refresh" button, the ComboBox gets populated by the list of known DocBases. 

Bellow is the code in the event handler of the button:

{% highlight csharp %}
comboBoxDocBase.Items.Clear();
try
{
    IDfClientX clientx = new DfClientX();
    IDfClient client = clientx.getLocalClient();
    IDfDocbaseMap docbaseMap = client.getDocbaseMap();

    int docbaseCount = myMap.getDocbaseCount();
    for (int i = 0; i < docbaseCount; i++)
    {
        comboBoxDocBase.Items.Add(docbaseMap.getDocbaseName(i));
    }
}
catch (Exception ex)
{
    // Log the exception and show the user a warning
}
{% endhighlight %}

This allowed me to easily allow average users to just select from a list of available servers, 
and advanced users can just enter the name of the DocBase.



