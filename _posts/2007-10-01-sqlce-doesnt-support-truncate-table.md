---
layout: post
title: "SqlCE doesn't support TRUNCATE TABLE"
date: 2007-10-01 15:54
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Databases, SqlCE]
share: true
redirect_from:
 - /2007-10-01-sqlce-doesnt-support-truncate-table/
 - /development/sqlce-doesnt-support-truncate-table/
---
Not long ago, I modified a piece of code to use SqlCE as a data store. The older code was using OleDb 
to access an MS Access file. As part of the modifications, our team did a general overview of much of 
the data access code. We did many changes, more than I can even number. Here is a partial list: 

* Review of table keys indices and restrictions.
* Usage of IDbCommand instead of SQL string statements.
* Removed legacy object model.
* Replaced consecutive DELETE and INSERT statements with UPDATE.
* and so on.

A few days ago, I noticed some delay in the data layer. This was nothing new - we process a huge amount 
of data - but since the many improvements, what used to be fast might now appear slow compared to the 
optimized code. 

After some search I've come up to a method that clears many tables in the database. Something along the lines of:

{% highlight csharp %}
using (IDbCommand cmd = DAL.GetClearTableCommand(tablename))
{
    // cmd.CommandText == "DELETE FROM " + tablename
    cmd.ExecuteNonQuery();
}
{% endhighlight %}

This of course, is nothing fancy or special. My thought was, instead of performing a DELETE statement, 
why not use the TRUNCATE TABLE statement, which is faster and more efficient. To my surprise, changing 
the `GetClearTableCommand()` method to return a `TRUNCATE TABLE` statement results in an parsing exception. 

After some research, I've found that the `TRUNCATE TABLE` statement is not supported/implemented in SqlCE.
Much to my disappointment, I will have to leave the DELETE statement in place, until I find a faster solution.
