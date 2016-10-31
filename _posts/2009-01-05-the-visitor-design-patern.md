---
title: "The Visitor design patern"
date: 2009-01-05 22:37
author: saguiitay
categories: [Development]
tags: [.Net, Design Patterns]
redirect_from:
 - /2009-01-05-the-visitor-design-patern/
 - /development/the-visitor-design-patern/
---
I've always been a huge advocat for design patterns in the past, but up until recently, I didn't get a chance to actually implement the 
[Visitor design pattern](http://en.wikipedia.org/wiki/Visitor_pattern).

This changed last week, when one of my colleagues asked me to implement a small tool, that handles one of our standard XML files. 
The tool was simple enough - scan the XML, and create dummy files based on information found the the XML. Nothing too fancy - just a small console application.

This triggered some light-bulb in my head - we've been manipulating those XMLs outside of our product for some time now. 
Mostly just for tests, or special clients requests. Instead of creating a one-time utility, I could create something much more useful - 
an infrastructure that will allow me to very quickly create any manipulation tool I'd like.

In comes the Visitor design pattern. I've very quickly created a Visitor abstract class, threw in a few classes that represent the various objects 
that are described in the XML, and voila - we're pretty much done.

The Visitor design pattern provides the infrastructure to "visit" an hierarchy of items, and "notify" the visitor when each item is handled 
(in my case, there's a Start-Children-End cycle). Users are now able to implement their own visitors, and handle the various "visits", 
by just overriding the virtual methods of the abstract Visitor.

From this point on, creating the requested tool took around 10 minutes - override the correct methods, retrieve the required information, and generate the dummy file.

More complex visitiors where just as easy to implement - removing content from the XML, adding new content, and even just running some statistics on the items in the XML.

For more details on design patterns, I strongly suggest reading AT LEAST on of the following books:

[Design Patterns: Elements of Reusable Object-Oriented Software (Addison-Wesley Professional Computing Series)](http://www.amazon.com/gp/product/0201633612?ie=UTF8&tag=biustudiesmat-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=0201633612)
 or 
[Head First Design Patterns](http://www.amazon.com/gp/product/0596007124?ie=UTF8&tag=biustudiesmat-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=0596007124)
