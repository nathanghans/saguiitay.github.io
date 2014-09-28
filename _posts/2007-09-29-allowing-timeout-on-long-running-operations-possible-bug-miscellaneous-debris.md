---
layout: post
title: "Allowing timeout on long-running operations - possible bug - Miscellaneous Debris"
date: 2007-09-29 15:55
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, development]
---
<span style="font-family:arial;">Avner Kashtan writes in his </span>[<span style="font-family:arial;">blog</span>](http://weblogs.asp.net/avnerk/default.aspx)<span style="font-family:arial;"> about an interesting problem, and solution, on how to run a long-running operation, with a timeout. The solution involves, obviously, running the code in a different thread. The possible bug is an unhandled exception in the different thread, which might kill the whole application process.</span>

<span style="font-family:arial;">The suggested solution is to catch the exception in the running thread, and "passing the exception backward". This is possible thanks to the use of an anonymous delegate. Although this is indeed, as mentioned in the post, an ugly solution, I find myself wondering what's the penalty of doing such a thing. Is the performance degraded so much? Is it such a horrible OOP crime? </span>[<span style="font-family:arial;">Allowing timeout on long-running operations - possible bug - Miscellaneous Debris</span>](http://weblogs.asp.net/avnerk/archive/2007/08/27/allowing-timeout-on-long-running-operations-possible-bug.aspx)

<span style="font-family:arial;"></span>



