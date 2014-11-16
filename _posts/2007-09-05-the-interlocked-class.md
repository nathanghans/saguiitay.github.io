---
layout: post
title: "The Interlocked class"
date: 2007-09-05 22:50
author: saguiitay
comments: true
categories: [Development]
tags: [.Net]
share: true
redirect_from:
 - /2007-09-05-the-interlocked-class/
 - /development/the-interlocked-class/
---
Working with threads and counters can because a really pain if you do not take care. 
Fortunetly for us, .Net supplies us with a couple of methods that can make our life a little bit easier. 
The class `Interlocked`, which can be found in `System.Thread` allows you to increment, decrement, and set the 
value of an int or long as an atomic action, thus preventing race conditions where multiple threads update the 
same counter at the same time. 

The first methods are of course, `Increment()` and `Decrement()`, both accepting an int or long variable and 
update it as requested. The next method is the `Exchange()`, which allow you to set the value of either an int, 
a long or object variable, by passing it the variable by reference, and the next requested value. 

Another interesting method in the `CompareExchange()`. This method checks if the value of the variable is as 
expected, and only then update it. The methos returns the current value of the variable, whether it was updated 
or not, allowing you to redo you calculations if the value changed while you were calculating the new value. 

Below is an example take from [MSDN](http://msdn.microsoft.com/library/en-us/cpref/html/frlrfsystemthreadinginterlockedclasscompareexchangetopic.asp):

{% highlight csharp %}
public int AddToTotal(int addend)
{
    int initialValue, computedValue;
    do
    {
        initialValue = totalValue;
        computedValue = initialValue + addend;
    }
    while (initialValue != Interlocked.CompareExchange(ref totalValue, computedValue, initialValue));
    return computedValue;
}
{% endhighlight %}
