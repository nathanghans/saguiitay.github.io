---
layout: post
title: "WordprocessingML: Part 2"
date: 2009-01-29 09:39
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, OpenXML, WordprocessingML]
share: true
redirect_from:
 - /2009/01/29/wordprocessingml-part-2/
 - /2009-01-29-wordprocessingml-part-2/
 - /2009-01-29-wordprocessingml-part-2
---
In the [first part of this serie]({% post_url 2009-01-21-wordprocessingml-part-1 %}), we've created a simple WordprocessingML document, 
and added some basic content to it. It is now time to add some formatting, to make that content presentable.

In this part we'll focus on the two basic methods to modify the formating of text:

- Runs properties
- Paragraph properties

More advanced methods, such as styles, will be covered later on.

# Runs properties

Going back to the sample from part 1, let's take a look at a text run:

{% highlight csharp %}
writer.WriteStartElement("r", wordmlNamespace);
writer.WriteStartElement("t", wordmlNamespace);
writer.WriteValue("Hello world");
writer.WriteEndElement(); // t
writer.WriteEndElement(); // r
{% endhighlight %}

After the start of the run (`r`) element, let's add a "run properties" element:

{% highlight csharp %}
writer.WriteStartElement("rPr", wordmlNamespace);
// formatting goes here
writer.WriteEndElement(); // rPr
{% endhighlight %}

Now we are free to define the format of the run. Attributes, such as bold, italic, underline are quite easy to define:

{% highlight csharp %}
writer.WriteElementString("b", wordmlNamespace, "");
writer.WriteElementString("i", wordmlNamespace, "");
writer.WriteElementString("u", wordmlNamespace, "");
{% endhighlight %}

More complex attributes, like the font size, color, or superscript/subscript are only slightly more interesting:

{% highlight csharp %}
writer.WriteStartElement("sz", wordmlNamespace);
writer.WriteAttributeString("val", wordmlNamespace, "8");
writer.WriteEndElement(); // sz

writer.WriteStartElement("color", wordmlNamespace);
writer.WriteAttributeString("val", wordmlNamespace, Colors.Red.ToArgb().ToString("X8").Substring(2));
writer.WriteEndElement(); // color

writer.WriteStartElement("vertAlign", wordmlNamespace);
writer.WriteAttributeString("val", wordmlNamespace, "superscript"); // subcript
writer.WriteEndElement(); // vertAlign
{% endhighlight %}

As you can see, defining the basic settings of the text is simple work.

# Paragraph properties

In a very similar way, we can define properties at the paragraph level:

{% highlight csharp %}
writer.WriteStartElement("pPr", wordmlNamespace);
// formatting goes here
writer.WriteEndElement(); // pPr
{% endhighlight %}

Alignment of the paragraph is defined using the "jc" (can anyone explain the JC name?!) element:

{% highlight csharp %}
writer.WriteStartElement("jc", wordmlNamespace);
writer.WriteAttributeString("val", wordmlNamespace, "right");
writer.WriteEndElement(); // jc
{% endhighlight %}

And indentation is just as easy (Note: All units in the fields are in TWIPS (1/20 of a point). There are 72 points to an inch and 20 TWIPS to a point, and therefore there are 72 * 20 TWIPS to an inch):

{% highlight csharp %}
writer.WriteStartElement("ind", wordmlNamespace);
writer.WriteAttributeString("firstLine", wordmlNamespace, "720");
writer.WriteAttributeString("hanging", wordmlNamespace, "1440");
writer.WriteEndElement(); // ind
{% endhighlight %}

and lines spacing:

{% highlight csharp %}
writer.WriteStartElement("spacing", wordmlNamespace);
writer.WriteAttributeString("line", wordmlNamespace, "120");
writer.WriteAttributeString("after", wordmlNamespace, "240");
writer.WriteAttributeString("before", wordmlNamespace, "360");
writer.WriteEndElement(); // spacing
{% endhighlight %}

That's it - basic formatting of text is quite simple, yet very powerful. There are a lot more options to control the formatting of textual components.
