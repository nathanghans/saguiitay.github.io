---
layout: post
title: "Compacting a RavenDb database"
date: 2014-09-04 20:07
author: saguiitay
comments: true
categories: [Development]
tags: [RavenDb]
share: true
---
One of my [RavenDb](http://ravendb.net) databases grew to an unrealistic size - 36GB when it contains around 500,000 documents which are mostly
small. After some back-and-forth with the RavenDb support team, I was suggested to compact the database. Sounds easy, if you know what you're
doing, since documentation on that is ... let's say lacking... Here's what you do, for my own future reference:

Using [cUrl](http://curl.haxx.se/), do a POST request to [http://\<servername\>/admin/compact?database=\<DatabaseName\>](http://<servername>/admin/compact?database=<DatabaseName>)

Here's a sample call:

{% highlight text %}
cUrl -F 'database=UsersDatabase' http://localhost:8080/admin/compact?database=UsersDatabase
{% endhighlight %}

Notice, that if you're using any form of authentication, you need to provide that too. Here's how it's done for NTLM Authentication:

{% highlight text %}
cUrl --ntlm --user username:password -F 'database=UsersDatabase' http://localhost:8080/admin/compact?database=UsersDatabase
{% endhighlight %}

<span style="color:#ff0000;text-decoration:underline;">**NOTICE: Your database will be off-line during the compact process, and won't be accessible.**</span>


