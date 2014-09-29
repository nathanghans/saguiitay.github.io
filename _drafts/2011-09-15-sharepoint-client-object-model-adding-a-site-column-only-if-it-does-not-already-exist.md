---
layout: post
title: "SharePoint Client Object Model: Adding a Site Column only if it does not already exist"
date: 2011-09-15 13:14
author: saguiitay
comments: true
categories: [Development, SharePoint]
tags: [.Net, Client Object Model, SharePoint]
---
Before creating a new Site Column, you should make sure that the column doesn't already exist. At the very least, make sure that the ID you are using isn't already taken. The sample below uses the FindField method from a [previous post](http://saguiitay.blogspot.com/2011/09/SharePoint-Client-Object-Model-Finding-a-field-in-a-list.html):

``` brush:
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
```



