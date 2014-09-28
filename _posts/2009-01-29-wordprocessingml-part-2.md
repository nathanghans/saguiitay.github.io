---
layout: post
title: "WordprocessingML: Part 2"
date: 2009-01-29 09:39
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, development, OpenXML, WordprocessingML]
---
In the [first part of this serie](http://saguiitay.blogspot.com/2009/01/wordprocessingml-part-1.html), we've created a simple WordprocessingML document, and added some basic content to it. It is now time to add some formatting, to make that content presentable.

In this part we'll focus on the two basic methods to modify the formating of text:

-   Runs properties
-   Paragraph properties

More advanced methods, such as styles, will be covered later on.

<span style="font-size:130%;">**Runs properties**</span>

Going back to the sample from part 1, let's take a look at a text run:

``` c#:html:nocontrols:nogutter
writer.WriteStartElement("r", wordmlNamespace);
writer.WriteStartElement("t", wordmlNamespace);
writer.WriteValue("Hello world");
writer.WriteEndElement(); // t
writer.WriteEndElement(); // r
```

After the start of the run ("r") element, let's add a "run properties" element:

``` c#:html:nocontrols:nogutter
writer.WriteStartElement("rPr", wordmlNamespace);
// formatting goes here
writer.WriteEndElement(); // rPr
```

Now we are free to define the format of the run. Attributes, such as bold, italic, underline are quite easy to define:

``` c#:html:nocontrols:nogutter
writer.WriteElementString("b", wordmlNamespace, "");
writer.WriteElementString("i", wordmlNamespace, "");
writer.WriteElementString("u", wordmlNamespace, "");
```

More complex attributes, like the font size, color, or superscript/subscript are only slightly more interesting:

``` c#:html:nocontrols:nogutter
writer.WriteStartElement("sz", wordmlNamespace);
writer.WriteAttributeString("val", wordmlNamespace, "8");
writer.WriteEndElement(); // sz

writer.WriteStartElement("color", wordmlNamespace);
writer.WriteAttributeString("val", wordmlNamespace, Colors.Red.ToArgb().ToString("X8").Substring(2));
writer.WriteEndElement(); // color

writer.WriteStartElement("vertAlign", wordmlNamespace);
writer.WriteAttributeString("val", wordmlNamespace, "superscript"); // subcript
writer.WriteEndElement(); // vertAlign
```

As you can see, defining the basic settings of the text is simple work.

<span style="font-size:130%;">**Paragraph properties**</span>

In a very similar way, we can define properties at the paragraph level:

``` c#:html:nocontrols:nogutter
writer.WriteStartElement("pPr", wordmlNamespace);
// formatting goes here
writer.WriteEndElement(); // pPr
```

Alignment of the paragraph is defined using the "jc" (can anyone explain the JC name?!) element:

``` c#:html:nocontrols:nogutter
writer.WriteStartElement("jc", wordmlNamespace);
writer.WriteAttributeString("val", wordmlNamespace, "right");
writer.WriteEndElement(); // jc
```

And indentation is just as easy (Note: All units in the fields are in TWIPS (1/20 of a point). There are 72 points to an inch and 20 TWIPS to a point, and therefore there are 72 \* 20 TWIPS to an inch):

``` c#:html:nocontrols:nogutter
writer.WriteStartElement("ind", wordmlNamespace);
writer.WriteAttributeString("firstLine", wordmlNamespace, "720");
writer.WriteAttributeString("hanging", wordmlNamespace, "1440");
writer.WriteEndElement(); // ind
```

and lines spacing:

``` c#:html:nocontrols:nogutter
writer.WriteStartElement("spacing", wordmlNamespace);
writer.WriteAttributeString("line", wordmlNamespace, "120");
writer.WriteAttributeString("after", wordmlNamespace, "240");
writer.WriteAttributeString("before", wordmlNamespace, "360");
writer.WriteEndElement(); // spacing
```

That's it - basic formatting of text is quite simple, yet very powerful. There are a lot more options to control the formatting of textual components.



