---
layout: post
title: "SharePoint Client Object Model: Finding a field in a list"
date: 2011-09-13 13:05
author: saguiitay
comments: true
categories: [Development, SharePoint]
tags: [.Net, Client Object Model, SharePoint]
---
When working with the SharePoint Client Object Model, it is useful to find a field in a list, or a site column in the web. Below are some extension methods to ClientContext, to facilitate these:

``` brush:
public static class FieldClientContextExtensions
{
    public static Field FindField(this ClientContext clientContext, List list,                                       
        Expression<Func<Field, bool>> predicate)
    {
        var webFields = list.Fields;
        var matchingFields = clientContext.LoadQuery(webFields.Where(predicate));
        clientContext.ExecuteQuery();

        Field field = matchingFields.FirstOrDefault();
        return field;
    }

    public static Field FindField(this ClientContext clientContext, Web web, Expression<Func<Field, bool>> predicate)
    {
        var webFields = web.Fields;
        var allFields = web.AvailableFields;
        var matchingWebFields = clientContext.LoadQuery(webFields.Where(predicate));
        var matchingAllFields = clientContext.LoadQuery(allFields.Where(predicate));

        clientContext.ExecuteQuery();
        Field field = matchingWebFields.FirstOrDefault() ?? matchingAllFields.FirstOrDefault();
        return field;
    }
}
```



