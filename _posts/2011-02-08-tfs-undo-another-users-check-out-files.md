---
title: "TFS: Undo another user's check-out files"
date: 2011-02-08 10:07
author: saguiitay
categories: [Development]
tags: [TFS]
redirect_from:
 - /2011-02-08-tfs-undo-another-users-check-out-files/
 - /development/tfs-undo-another-users-check-out-files/
---
In order to undo files that are checked-out by another user, ou can use the TF.EXE command-line tool:

{% highlight text %}
tf undo [/workspace:workspacename[;workspaceowner]] [/recursive] itemspec [/noprompt] [/login:username,[password]] [/collection:TeamProjectCollectionUrl]
{% endhighlight %}
 
For example:
{% highlight text %}
tf undo /workspace:MyWorkspace;DOMAIN\\Itay /recursive \*.\* /collection:TeamProject
{% endhighlight %}

You can find the TF.EXE tool in the following locations:
- For TFS 2008: `C:\Program Files (x86)\Microsoft Visual Studio 9.0\Common7\IDE\TF.exe`
- For TFS 2010: `C:\Program Files (x86)\Microsoft Visual Studio 10.0\Common7\IDE\TF.exe`



