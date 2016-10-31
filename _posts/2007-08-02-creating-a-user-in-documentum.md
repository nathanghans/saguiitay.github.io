---
title: "Creating a user in Documentum"
date: 2007-08-02 18:38
author: saguiitay
categories: [Development]
tags: [Documentum]
redirect_from:
 - /2007-08-02-creating-a-user-in-documentum/
 - /development/creating-a-user-in-documentum/
---
Here's 2 ways to create a user in Documentum: Using the DQL:

{% highlight sql %}
create dm_cabinet object
set object_name = '%USERNAME%'
go

create dm_user object
set user_name = '%USER DISPLAY NAME%,
set default_folder = '/%USERNAME%',
set user_os_name = '%USERNAME%',
set user_privileges = %PRIVILEGES%',
set user_address = '%USER EMAIL%
go
{% endhighlight %}

Using the IAPI:

{% highlight sql %}
create,c,dm_user
set,c,l,user_name
set,c,l,user_os_name
set,c,l,user_address
save,c,l
{% endhighlight %}



