---
layout: post
title: "Version comments for a Documentum object"
date: 2009-01-08 11:34
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Documentum]
share: true
redirect_from:
 - /2009/01/08/version-comments-for-a-documentum-object/
 - /2009-01-08-version-comments-for-a-documentum-object/
 - /2009-01-08-version-comments-for-a-documentum-object
---
Retrieving the version comments of a Documentum SysObject is an easy task:

{% highlight csharp %}
    private static string GetVersionsComment(IDfSysObject dfObj)
    {
        StringBuilder sb = new StringBuilder();

        if (dfObj.getVersionLabelCount() > 0)
        {
            for (int i = 0; i < dfObj.getVersionLabelCount(); i++)
            {
                string versionLabel = dfObj.getVersionLabel(i);
                sb.AppendLine(versionLabel);
            }
            sb.AppendLine(dfObj.getLogEntry());
        }
        return sb.ToString().Trim();
    }
{% endhighlight %}
