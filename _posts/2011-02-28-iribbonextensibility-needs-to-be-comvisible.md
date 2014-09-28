---
layout: post
title: "IRibbonExtensibility needs to be ComVisible"
date: 2011-02-28 16:55
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, development, outlook, VSTO]
---
When developing an Outlook add-in using VSTO 4, if you are implementing the IRibbonExtensibility interface (for customizing a Ribbon, for example) you might encounter cases where your Ribbon or menu items don't appear in Outlook's UI.
 In those cases, you should check the following things first:
1.  Your XML is valid - invalid Ribbon XML, such as placing the <span style="font-family:&quot;"></span>node inside the <span style="font-family:&quot;"></span>node are common mistakes, which will prevent your controls from appearing
2.  Your class should be marks as ComVisible:
    <span style="font-family:&quot;">[ComVisible(true)]
    public class Ribbon : IRibbonExtensibility
    {
       ...
    }</span>



