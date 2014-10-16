---
layout: post
title: "IRibbonExtensibility needs to be ComVisible"
date: 2011-02-28 16:55
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Outlook, VSTO]
share: true
redirect_from:
 - /2011/02/28/iribbonextensibility-needs-to-be-comvisible/
 - /2011-02-28-iribbonextensibility-needs-to-be-comvisible/
 - /2011-02-28-iribbonextensibility-needs-to-be-comvisible
---
When developing an Outlook add-in using VSTO 4, if you are implementing the `IRibbonExtensibility` interface (for customizing a Ribbon, 
for example) you might encounter cases where your Ribbon or menu items don't appear in Outlook's UI.

In those cases, you should check the following things first:
1. Your XML is valid - invalid Ribbon XML will prevent your controls from appearing
2. Your class should be marks as ComVisible:
   {% highlight csharp %}
   [ComVisible(true)]
   public class Ribbon : IRibbonExtensibility
   {
       //...
   }
   {% endhighlight %}



