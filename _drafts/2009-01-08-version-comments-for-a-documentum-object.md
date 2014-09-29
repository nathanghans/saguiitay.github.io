---
layout: post
title: "Version comments for a Documentum object"
date: 2009-01-08 11:34
author: saguiitay
comments: true
categories: [Development, Documentum]
tags: [.Net, development, Documentum]
---
Retrieving the version comments of a Documentum SysObject is an easy task:<span style="font-family:courier new;"> </span>

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



