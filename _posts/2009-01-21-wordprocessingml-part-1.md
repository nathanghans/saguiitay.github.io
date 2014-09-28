---
layout: post
title: "WordprocessingML: Part 1"
date: 2009-01-21 21:32
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, development, OpenXML, WordprocessingML]
---
Let's start with a public notice: Most of the OpenXML/WordprocessingML samples I've found word with TextWriters, and just write XML strings into the writer. Me, being the funny guy I am, prefer to work with XmlWriters. This makes sure I make no structure mistakes in the XML itself, allows me to generate well-formatted XML (useful during development), and just seems more "natural" to me.

Ok. Now that we got that out of the way, let's start with the very basics - creating an empty Docx file:

<span style="font-size:130%;">**Creating a WordprocessingDocument object:** </span><span style="font-size:130%;">****</span>

Nothing can be more simple than this. Just call the static method "Create" of the type WordprocessingDocument, provide a filename or stream, and select the type of document you want to create. There are several types of documents, defined in the WordprocessingDocumentType enumeration. For more details on this, just go to <http://msdn.microsoft.com/en-us/library/documentformat.openxml.wordprocessingdocumenttype.aspx>

Here's the snippet:

``` c#:html:nocontrols:nogutter
using (WordprocessingDocument wpd = WordprocessingDocument.Create(filename, WordprocessingDocumentType.Document))
{
}
```

Notice how I used the "using" directive - WordprocessingDocument implements the IDisposable interface...

**<span style="font-size:130%;">Adding the main part:</span>**

Each document consist of multiple parts, the "main" part being the document content itself. other types (which I'll cover in future entries) include styles, numbering, properties and settings. There's nothing exciting in this part - we just ask our document to create a "main" part for itself, and we keep a reference to that part.

``` c#:html:nocontrols:nogutter
MainDocumentPart mainPart = wpd.AddMainDocumentPart();
```

**<span style="font-size:130%;">Getting an XML writer:</span>**

Each part implements a "GetStream" method, so this is mostly boiler plate code:

``` c#:html:nocontrols:nogutter
XmlWriterSettings settings = new XmlWriterSettings();
settings.Indent = true;
settings.IndentChars = "\t";
settings.Encoding = _new UTF8Encoding();

using (Stream stream = mainPart.GetStream())
using (XmlWriter xmlWr = XmlWriter.Create(stream, settings))
{
}
```

**<span style="font-size:130%;">Adding content:</span>**

I'm not going to go too deeply in this section - you can find various samples explaining the full structure of content in documents. For now, let's just say that text goes into paragraphs. Paragraphs consist of runs, and runs contain text.

``` c#:html:nocontrols:nogutter
string wordmlNamespace = "http://schemas.openxmlformats.org/wordprocessingml/2006/main";
writer.WriteStartElement("p", wordmlNamespace);
writer.WriteStartElement("r", wordmlNamespace);
writer.WriteStartElement("t", wordmlNamespace);
writer.WriteValue("Hello word"); // NOT A TYPO! :)
writer.WriteEndElement(); // t
writer.WriteEndElement(); // r
writer.WriteEndElement(); // p
```

That's it - you're free to add content to your document as you see fit. Just don't forget to call the "Close" method of the WordprocessingDocument instance, in order to save the file.



