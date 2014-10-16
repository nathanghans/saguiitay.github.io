---
layout: post
title: "MIME Types and File Extensions"
date: 2008-01-29 15:53
author: saguiitay
comments: true
categories: [Development]
tags: [.Net]
share: true
redirect_from:
 - /2008/01/29/mime-types-and-file-extensions/
---
Whenever working with content of files, it is often useful to have a way to find the MIME type based on a file extension,
or the other way around - finding the file extension from a MIME type. Below are 2 useful methods for such requirements:

{% highlight csharp %}
public static string GetExtensionFromMime(string mimeType)
{
    try
    {
        RegistryKey key = Registry.ClassesRoot.OpenSubKey(@"Mime\Database\Content Type\" + mimeType);
        if (key == null)
            return null;

        string str = key.GetValue("Extension") as string;
        if (string.IsNullOrEmpty(str))
            return string.Empty;
        
        return str;
    }
    catch
    {
        return string.Empty;
    }
}

public static string GetMimeFromExtension(string ext)
{
    if (!ext.StartsWith("."))
        ext = "." + ext;
    RegistryKey key = Registry.ClassesRoot.OpenSubKey(ext);
    if (key == null)
        return null;

    return key.GetValue("Content Type") as string;
}
{% endhighlight %}
