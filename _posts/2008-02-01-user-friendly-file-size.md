---
layout: post
title: "User friendly file size"
date: 2008-02-01 15:52
author: saguiitay
comments: true
categories: [Development]
tags: [.Net]
share: true
redirect_from:
 - /2008/02/01/user-friendly-file-size/
 - /2008-02-01-user-friendly-file-size/
 - /2008-02-01-user-friendly-file-size
---
Based on a utility method found in the `Microsoft.SharePoint.Utilities.SPUtility` class, here's a method to get a user-friendly text for a file size:

{% highlight csharp %}
public static string FormatSize(long cbSize)
{
    double num;
    if (cbSize <= 1024L)
    {
        if (cbSize <= 0L)
        {
            return string.Format("{0} KB", "0");
        }
        return string.Format("< 1 KB");
    }
    if (cbSize <= 1048576L)
    {
        num = Math.Round((double)(Convert.ToDouble(cbSize) / 1024.0), 1);
        return string.Format("{0} KB", num);
    }
    if (cbSize <= 1073741824L)
    {
        num = Math.Round((double)(Convert.ToDouble(cbSize) / 1048576.0), 1);
        return string.Format("{0} MB", num);
    }

    if (cbSize <= 1099511627776L)
    {
        num = Math.Round((double)(Convert.ToDouble(cbSize) / 1073741824.0), 1);
        return string.Format("{0} GB", num);
    }

    num = Math.Round((double)(Convert.ToDouble(cbSize) / 1099511627776.0), 1);
    return string.Format("{0} TB", num);
}
{% endhighlight %}



