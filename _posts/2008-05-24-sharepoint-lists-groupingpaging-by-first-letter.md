---
title: "SharePoint Lists: Grouping/Paging by first letter"
date: 2008-05-24 21:08
author: saguiitay
categories: [Development]
tags: [SharePoint]
redirect_from:
 - /2008-05-24-sharepoint-lists-groupingpaging-by-first-letter/
 - /development/sharepoint-lists-groupingpaging-by-first-letter/
---
A very interesting blog entry, 
[Alphabetical Paging on a SharePoint List View>](http://www.tqcblog.com/archive/2008/02/15/alphabetical-paging-on-a-sharepoint-list-view.aspx), 
by [Tom Clarkson](http://www.tqcblog.com/), talks about grouping/paging items in a SharePoint list based on their first letter.

If you are just looking for the formula needed to create a calculated column that contains the first letter of the item's Title, here it is:

{% highlight sql %}
=IF(ISNUMBER(VALUE(LEFT(Title,1))), "0", LEFT(Title,1))
{% endhighlight %}




