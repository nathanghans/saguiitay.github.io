---
title: "Retrieving extended permissions in Documentum with .Net"
date: 2007-09-12 18:40
author: saguiitay
categories: [Development]
tags: [.Net, Documentum]
redirect_from:
 - /2007-09-12-retrieving-extended-permissions-in-documentum-with-net/
 - /development/retrieving-extended-permissions-in-documentum-with-net/
---
Following version 5 of the Documentum Content Server, security entities can have extended permissions on items. 
Those extended permissions include: Execute Procedure, Change Location, Change State, Change Permission and Change Ownership.
In order to retrieve those permissions by code, it is required to manually check for those permissions. 
Assuming that you have the object ID of an item, here's the .Net code in order to know if the user has those extended permissions:

{% highlight csharp %}
IDfId itemIdObj = null;
IDfSysObject itemSysObj = null;
IDfACL aclObj = null;
string itemId = null;
try
{
    // Get the Id object of the item
    itemIdObj = _clientx.getId(itemId);
    // Get the item itself
    itemSysObj = (IDfSysObject)_session.getObject(itemIdObj);
    // Get the ACL of the item
    aclObj = itemSysObj.getACL();
    // Get extended permissions for entity i. This code should be run for each entity
    int xperms = aclObj.getAccessorXPermit(i);
    if ((xperms & 1) == 1)
    {
        // User has the "Execute Procedure"
    }
    if ((xperms & 2) == 2)
    {
        // User has the "ChangeLocation"
    }
    if ((xperms & 32768) == 32768)
    {
        // User has the "Change State"
    }
    if ((xperms & 65536) == 65536)
    {
        // User has the "Change Permission"
    }
    if ((xperms & 131072) == 131072)
    {
        // User has the "Change Ownership"
    }
}
catch (Exception ex)
{
    // Log exception
}
{% endhighlight %}
