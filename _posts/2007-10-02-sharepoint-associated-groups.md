---
layout: post
title: "SharePoint Associated groups"
date: 2007-10-02 13:25
author: saguiitay
comments: true
tags: [SharePoint]
share: true
redirect_from:
 - /2007/10/02/sharepoint-associated-groups/
 - /2007-10-02-sharepoint-associated-groups/
 - /2007-10-02-sharepoint-associated-groups
---
When you create a new sub-site in SharePoint, and choose the "Use Unique Permissions" option, three groups are 
created at the site-collection level: Owners, Members & Visitors. These groups are "associated" to the sub-site that you've just created.

But what does "associated" actually mean?

First, I misled you a bit - the names of the groups are actually _SiteName_ Owners, _SiteName_ Members & _SiteName_ Visitors (where _SiteName_ is the name of the sub-site you're creating).

Next, they all have a nice link in their description, directing you to the sub-site.

More interesting, those three groups get some permissions on the sub-site.

The less obvious "associations" are the fact that those groups will appear in the Quick Launch area when you go to the "People and Groups" section of the site. 
Also, if you delete the sub-site, the groups will be removed from the site-collection.

Lastly, when you add a user to the site, you can either assign the user an explicit permission level, or you can add her to one of the *associated groups of the site.*

That's quite a lot of meaning for a single word. Let look a bit deeper into each meaning:

## Title & Description:

This is just some basic naming policy used by SharePoint. The _SiteName_ is validated, the group type is appended, the description is generated with a link. Nothing fancy here.

## Permissions:

The permissions are obtained by assigning permission levels to each group. Again, nothing too interesting.

## Quick Launch:

The list of groups that appear in the Quick Launch is retained in the 
[AssociatedGroups](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.associatedgroups.aspx) property of the 
[SPWeb](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.aspx). 
This list can also be updated by modifying the `vti_associategroups` key in the [AllProperties](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.allproperties.aspx)
property of [SPWeb](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.aspx).

Setting a group as the [AssociatedMemberGroup](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.associatedmembergroup.aspx),
[AssociatedVisitorGroup](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.associatedvisitorgroup.aspx) or
[AssociatedOwnerGroup](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.associatedownergroup.aspx) automatically
adds the group to the list of AssociatedGroups. 

These properties can be also updated by modifying the `vti_associatemembergroup`, `vti_associatevisitorgroup` & `vti_associateownergroup`
keys in the [AllProperties](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.allproperties.aspx) property of 
[SPWeb](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.aspx). These three properties are also reflected in the 
"Set up Groups" page of the sub-site.

## Auto deletion:

This issue is a bit more complex. 

When using the SharePoint UI, or when calling [CreateDefaultSharePointGroups](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.createdefaultassociatedgroups.aspx)
methods of [SPWeb](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.aspx), this behavior is implemented automatically.
However, creating a group, and even associating it with a site, doesn't reproduce it. 

Only after using [Lutz Roeder's .Net Reflector](http://www.aisto.com/roeder/dotnet), and examining the [CreateDefaultSharePointGroups](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.createdefaultassociatedgroups.aspx)
method, I've found that you need to add the group's ID to the `vti_createdassociategroups` key in 
[AllProperties](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.allproperties.aspx).

## Adding Users:

When adding a user to a sub-site, only the groups listed in AssociatedGroups are listed as possible groups for the new user.



