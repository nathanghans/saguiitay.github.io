---
layout: post
title: "SharePoint 2007 Web Folders"
date: 2008-04-28 12:21
author: saguiitay
comments: true
categories: [Development]
tags: [SharePoint, Web Folder]
share: true
redirect_from:
 - /2008/04/28/sharepoint-2007-web-folders/
 - /2008-04-28-sharepoint-2007-web-folders/
 - /2008-04-28-sharepoint-2007-web-folders
---
I've been working with SharePoint 2007 for so long now on my office desktop and laptop, that it seems 
that I don't remember some of the SharePoint 2007 requirements. 

Working on a fresh virtual machine that has WSS 3.0 installed and running, I've tries to open a 
Pages Library as a Web Folder. The result was a nice error message telling me "Your client does not support 
opening this list with Windows Explorer." 

This seemed a bit strange, since I'm working on the SharePoint server itself, which is a fully updated
Windows Server 2003 (all patch and updates installed), and I'm browsing using Internet Explorer 7 (again, fully updated).

After ~~googling~~ binging a bit, it seems the resolution was hidden in a forum with a link on page 2 of Google.
The topic forwarded me to the following Microsoft page: [Software Update for Web Folders (KB907306)](http://www.microsoft.com/downloads/details.aspx?FamilyId=17C36612-632E-4C04-9382-987622ED1D64&displaylang=en)

I'm not sure if I should say that this update should come as part of the Windows Update or not, but I'm sure glad I've found this!


