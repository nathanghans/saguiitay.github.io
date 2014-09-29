---
layout: post
title: "SharePoint Associated groups"
date: 2007-10-02 13:25
author: saguiitay
comments: true
categories: [Development, SharePoint]
tags: [EMC, MOSS, SharePoint]
---
<span style="font-family:arial;">When you create a new sub-site in SharePoint, and choose the "Use Unique Permissions" option, three groups are created at the site-collection level: Owners, Members & Visitors. These groups are "associated" to the sub-site that you've just created.</span>

<span style="font-family:arial;">But what does "associated" actually mean?</span>

<span style="font-family:arial;">First, I misled you a bit - the names of the groups are actually SiteName Owners, SiteName Members & SiteName Visitors (where SiteName is the name of the sub-site you're creating). </span>

<span style="font-family:arial;">Next they all have a nice link in their description, directing you to the sub-site.</span>

<span style="font-family:arial;">More interesting, those three groups get some permissions on the sub-site.</span>

<span style="font-family:arial;">The less obvious "associations" are the fact that those groups will appear in the Quick Launch area when you go to the "People and Groups" section of the site. Also, if you delete the sub-site, the groups will be removed from the site-collection.</span>

<span style="font-family:arial;">Lastly, when you add a user to the site, you can either assign the user an explicit permission level, or you can add her to onw of the a*ssociated groups of the site.* </span>

<span style="font-family:arial;">That's quite a lot of meaning for a single word. Let look a bit deeper into each meaning:</span>

<span style="font-family:arial;">**Title & Description:** This is just some basic naming policy used by SharePoint. The SiteName is validated, the group type is appended, the description is generated with a link. Nothing fancy here.</span>

<span style="font-family:arial;">**Permissions:** The permissions are obtained by assigning permission levels to each group. Again, nothing too interesting.</span>

<span style="font-family:arial;">**Quick Launch:**</span>[](http://tk3.storage.msn.com/y1pm1Ce_ywp6TtYSCAnZsdaogAN-wAVwnKWS9LkgwgiAbt1QI03_QEBjC3ErNdsNrfYFW39vVm4k8ywBq0484S0Uw?PARTNER=WRITER)<span style="font-family:arial;"> **[](http://bp0.blogger.com/_1hEV2q8PWSg/SC7QMb9DJVI/AAAAAAAAAAM/haryhBAkHgU/s1600-h/SetUpGroups.jpg)** The list of groups that appear in the Quick Launch is retained in the </span>[<span style="font-family:arial;">AssociatedGroups</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.associatedgroups.aspx)<span style="font-family:arial;"> property of the </span>[<span style="font-family:arial;">SPWeb</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.aspx)<span style="font-family:arial;">. This list can also be updated by modifying the vti\_associategroups key in the </span>[<span style="font-family:arial;">AllProperties</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.allproperties.aspx)<span style="font-family:arial;"> property of </span>[<span style="font-family:arial;">SPWeb</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.aspx)<span style="font-family:arial;">. Setting a group as the </span>[<span style="font-family:arial;">AssociatedMemberGroup</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.associatedmembergroup.aspx)<span style="font-family:arial;">, </span>[<span style="font-family:arial;">AssociatedVisitorGroup</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.associatedvisitorgroup.aspx)<span style="font-family:arial;"> or </span>[<span style="font-family:arial;">AssociatedOwnerGroup</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.associatedownergroup.aspx)<span style="font-family:arial;"> automatically adds the group to the list of AssociatedGroups. These properties can be also updated by modifying the vti\_associatemembergroup, vti\_associatevisitorgroup & vti\_associateownergroup keys in the </span>[<span style="font-family:arial;">AllProperties</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.allproperties.aspx)<span style="font-family:arial;"> property of </span>[<span style="font-family:arial;">SPWeb</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.aspx)<span style="font-family:arial;">. These three properties are also reflected in the "Set up Groups" page of the sub-site.</span>

<span style="font-family:arial;">**Auto deletion:** This issue is a bit more complex. When using the SharePoint UI, or when calling </span>[<span style="font-family:arial;">CreateDefaultSharePointGroups</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.createdefaultassociatedgroups.aspx)<span style="font-family:arial;"> methods of </span>[<span style="font-family:arial;">SPWeb</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.aspx)<span style="font-family:arial;">, this behavior is implemented automatically. However, creating a group, and even associating it with a site, doesn't reproduce it. Only after using </span>[<span style="font-family:arial;">Lutz Roeder's .Net Reflector</span>](http://www.aisto.com/roeder/dotnet)<span style="font-family:arial;">, and examining the </span>[<span style="font-family:arial;">CreateDefaultSharePointGroups</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.createdefaultassociatedgroups.aspx)<span style="font-family:arial;"> method, I've found that you need to add the group's ID to the vti\_createdassociategroups key in </span>[<span style="font-family:arial;">AllProperties</span>](http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spweb.allproperties.aspx)<span style="font-family:arial;">.</span>

<span style="font-family:Arial;">**Adding Users:** </span><span style="font-family:Arial;">When adding a user to a sub-site, only the groups listed in AssociatedGroups are listed as possible groups for the new user.</span>



