---
layout: post
title: "SharePoint Client Object Model: Adding a Site Column only if it does not already exist"
date: 2011-09-15 13:14
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Client Object Model, SharePoint]
share: true
redirect_from:
 - /2011/09/15/sharepoint-client-object-model-adding-a-site-column-only-if-it-does-not-already-exist/
---
Before creating a new Site Column, you should make sure that the column doesn't already exist. 
At the very least, make sure that the ID you are using isn't already taken. 
The sample below uses the FindField method from a [previous post]({% post_url 2011-09-14-sharepoint-client-object-model-verifying-if-a-field-is-part-of-a-content-type %}):

{% highlight csharp %}
public static Field EnsureField(this ClientContext clientContext, Web web, Guid id, string name, string schema)
{
    var field = FindField(clientContext, web, f => f.Id == id);
    if (field != null)
        return field;

    var newField = web.Fields.AddFieldAsXml(schema, false,
        AddFieldOptions.AddToNoContentType | AddFieldOptions.AddFieldInternalNameHint);
    clientContext.ExecuteQuery();
    return newField;
}
{% endhighlight %}
