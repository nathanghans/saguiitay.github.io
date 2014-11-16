---
layout: post
title: "Connecting to Documentum using .Net"
date: 2007-09-13 21:00
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Documentum]
share: true
redirect_from:
 - /2007-09-13-connecting-to-documentum-using-net/
 - /development/connecting-to-documentum-using-net/
---
I've spoke in an earlier post about working with Documentum in .Net. In this post, I'll show you how to 
connect to a DocBase, and get the list of cabinets. The following code will connect to a DocBase. 
It assumes that you have a Username, Password and DocBase variables declared that contain valid information:

{% highlight csharp %}
// Get a client object 
DfClientX _clientx = new DfClientX(); 
IDfClient _client = _clientx.getLocalClient(); 

if (_client == null) 
    throw new Exception("Failed creating Documentum client"); 

// Retrieve the client's version 
Console.WriteLine("Using DFC version '{0}'", _clientx.getDFCVersion()); 

// Create an object with the credentials of the user
IDfLoginInfo _loginInfoObj = _clientx.getLoginInfo(); 
_loginInfoObj.setUser(Username); 
_loginInfoObj.setPassword(Password); 

// Create a new session to the requested DocBase 
IDfSession _session = _client.newSession(DocBase, _loginInfoObj); 
if (_session == null && !_session.isConnected()) 
{ 
    Console.WriteLine("Failed conecting to Documentum"); 
    if (_session != null) 
    { 
        Console.WriteLine("DFC Messages:\r\n{0}", _session.getMessage(1)); 
    } 
    return; 
} 
Console.WriteLine("Using server version '{0}'", _session.getServerVersion()); 
{% endhighlight %}

Now, once we're connected to the Documentum DocBase, we'll list all the cabinets:

{% highlight csharp %}
IDfQuery query = _clientx.getQuery();
// Quering the "dm_cabinet" table returns only items of dm_cabinet type
query.setDQL("SELECT r_object_id, object_name, title FROM dm_cabinet");

// Query the session for the cabinets
IDfCollection col = query.execute(_session, (int)DFCLib.tagDfQueryTypes.IDfQuery_DF_READ_QUERY);

// Loop through all the items in the collection
while (col.next())
{
    // Get the current item from the collection
    IDfTypedObject typedObj = col.getTypedObject();
    // Print the item's name
    Console.WriteLine("Cabinet name: {0}", typedObj.getString("object_name"))
}
col.Close();
{% endhighlight %}

One of the most important thing to remember, is that you have to close the `IDfCollection`. 
Each session has a very limited number of collections it can have open at the same time. 
If you need more collections, I would suggest just caching the items inside a .Net collection for later use.



