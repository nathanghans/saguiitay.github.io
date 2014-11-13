---
layout: post
title: "SharePoint Client Object Model: Verifying if a Field is part of a Content Type"
date: 2011-09-14 13:08
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Client Object Model, SharePoint]
share: true
redirect_from:
 - /2011/09/14/sharepoint-client-object-model-verifying-if-a-field-is-part-of-a-content-type/
 - /2011-09-14-sharepoint-client-object-model-verifying-if-a-field-is-part-of-a-content-type/
 - /development/sharepoint-client-object-model-verifying-if-a-field-is-part-of-a-content-type/
---
Before adding a field to a ContentType, it is important to check that the field is not already part of the Content Type. 
Here's an extension method to ClientContext that does just that:

{% highlight csharp %}
public static bool ExistInContentType( this ClientContext clientContext, ContentType contentType, Field field)
{
    var fieldLinks = contentType.FieldLinks;
    var existingFields = clientContext.LoadQuery(fieldLinks.Where(fl => fl.Id == field.Id));
    clientContext.ExecuteQuery();
    var existingField = existingFields.FirstOrDefault();
    if (existingField != null)
        return true;
    return false;
}
{% endhighlight %}
