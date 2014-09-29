---
layout: post
title: "Working with MAPI Items"
date: 2007-09-06 08:27
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, development, mapi, outlook]
---
<span style="font-family:arial;">While the Outlook objects expose most of the properties of the items they represent, sometime they miss the exact field that you need. In this post I'll show you the very basics in retrieving information from the MAPI object that your CDO object represent. Assuming that you have an Outlook mail item, you need to convert it to a MAPI object. First well need to have a MAPI.Session object:</span>

``` c#:html:nocontrols:nogutter
MAPI.Session session = new MAPI.SessionClass();
session.Logon(null, null, false, false, null, true, Missing.Value);
```

<span style="font-family:arial;">And now to the actual conversion, which is basically a request for the MAPI object, according to its EntryID:</span>

``` c#:html:nocontrols:nogutter
MAPI.Message message = (MAPI.Message)session.GetMessage(item.EntryID, Missing.Value);
```

<span style="font-family:arial;">Now that we have our MAPI.Message, well get all its properties (called fields), then well fish out of them the specific required field. After that, you can just get the fields value, and type:</span>

``` c#:html:nocontrols:nogutter
MAPI.Fields fields = message.Fields as MAPI.Fields;
MAPI.Field field = fields.get_Item(MAPI.CdoPropTags.CdoPR_SUBJECT, Missing.Value) as MAPI.Field;
Console.WriteLine(field.Type.ToString());
Console.WriteLine(field.Value.ToString());
```

<span style="font-family:arial;">In the above example we've retrieved the items subject. Nothing fancy here, since we could have just as easily get that from the Outlook object. However, if you'll have a closer look at the MAPI.CdoPropTags enumeration, you'll be able to find many more fields that are not exposed. You can see the values of the enum and the meanings of the different type in a file called MAPITags.h</span>



