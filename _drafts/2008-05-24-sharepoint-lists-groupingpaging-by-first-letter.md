---
layout: post
title: "SharePoint Lists: Grouping/Paging by first letter"
date: 2008-05-24 21:08
author: saguiitay
comments: true
categories: [Development, SharePoint]
tags: [Microsoft, MOSS, SharePoint]
---
<span style="font-family:arial;">A very interesting blog entry, </span>[<span style="font-family:arial;">Alphabetical Paging on a SharePoint List View</span>](http://www.tqcblog.com/archive/2008/02/15/alphabetical-paging-on-a-sharepoint-list-view.aspx)<span style="font-family:arial;">, by </span>[<span style="font-family:arial;">Tom Clarkson</span>](http://www.tqcblog.com/)<span style="font-family:arial;">, talks about grouping/paging items in a SharePoint list based on their first letter.</span> <span style="font-family:arial;"></span> <span style="font-family:arial;">If you are just looking for the formula needed to create a calculated column that contains the first </span><span style="font-family:arial;">letter of the item's Title, here it is:</span>
<span style="font-family:courier new;">=IF(ISNUMBER(VALUE(LEFT(Title,1))), "0", LEFT(Title,1))</span>



