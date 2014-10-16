---
layout: post
title: "WordprocessingML to the rescue"
date: 2009-01-15 22:41
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Aspose, OpenXML, WordprocessingML]
share: true
redirect_from:
 - /2009/01/15/wordprocessingml-to-the-rescue/
---
I've recently found myself in need to create documents out of various sources (other documents, information from ECM system, and so on). 
I've tried several approaches, including:

-   Word Object Model
-   HTML generation
-   RTF generation
-   Aspose.Words
-   OpenXML

My first try was with the Microsoft Word Object Model, but encountered several bumpers: The complexity of the object model, 
the requirement of having Microsoft Word installed on the client machine, the non-fluent code - all of those made the whole 
experience something I'd rather forget.

Next, I tried generating HTML and RTF documents. HTML proved quite simple, but was a bit limited for my requirements, and 
generating a single-file HTML (MHT) would have required much too manual work to my liking. RTF, with it's specification proved 
just nasty, for lack of a better word.

At first I was relunctant to use a third part component, such as [Aspose.Words](http://www.aspose.com/categories/file-format-components/aspose.words-for-.net-and-java/default.aspx).
The component is quite easy to learn, and quite powerful, but was lacking some of the deep requirements (such as formatting tables, embedding objects, etc), 
and therefore I had to drop it. I'm still planning to use it as a format-converting component, as it allow to easily convert between HTML, PDF, DOC, DOCX and 
so on, while retaining a high level of fidelity.

Lastly, I tackled the OpenXML SDK. To tell you the truth, I wasn't too happy to go there in the beginning - the SDK seems simple enough, 
be requires _A LOT_ of manual work with XML - not very user friendly or code-efficient. However, to my surprise, the Markup Language Reference 
was quite easy to use; the OpenXML format is EXTREMELY powerful (allowing me to do even more than I planned).

Although I am still experiencing some problems in generating numbered paragraphs and such, after a single day of playing with it, 
I find myself quite comfortable with the OpenXML format, and confident it will suite my needs.

Some useful resources for getting started with OpenXML:

- [OpenXML SDK](http://www.microsoft.com/downloads/details.aspx?FamilyId=AD0B72FB-4A1D-4C52-BDB5-7DD7E816D046&displaylang=en)
- [How Do I ... guides](http://msdn.microsoft.com/en-us/library/bb491088.aspx)
- [OpenXML Developer](http://openxmldeveloper.org/default.aspx)
- [OpenXML Markup Language Reference](http://www.ecma-international.org/publications/files/ECMA-ST/Office%20Open%20XML%201st%20edition%20Part%204%20(PDF).zip) from the Ecma International

As I continue my research and work, I'll try posting some code samples and guides.
