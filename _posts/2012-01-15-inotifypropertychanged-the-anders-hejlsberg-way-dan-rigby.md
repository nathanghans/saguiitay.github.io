---
layout: post
title: "INotifyPropertyChanged, the Anders Hejlsberg Way | Dan Rigby"
date: 2012-01-15 15:00
author: saguiitay
comments: true
categories: [Development]
tags: [.Net]
share: true
redirect_from:
 - /2012/01/15/inotifypropertychanged-the-anders-hejlsberg-way-dan-rigby/
---
Dan Rigby brings in his blog a nice implementation of [INotifyPropertyChanged](http://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanged.aspx) -
taken from Andres Hejlsberg: [INotifyPropertyChanged, the Anders Hejlsberg Way](http://danrigby.com/2012/01/08/inotifypropertychanged-the-anders-hejlsberg-way/)

Add this to Andreas Kahler's solution of using an `Expression<Func<T>>` parameter to avoid "magic strings" that can be found 
here: [Implementing INotifyPropertyChanged without hard-coded strings](http://blog.andreaskahler.com/2009/03/implementing-inotifypropertychanged.html).

Together, these two solutions result in an easy to use, simple to understand, and change-safe implementation of `INotifyPropertyChanged`.

