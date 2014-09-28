---
layout: post
title: "The Interlocked class"
date: 2007-09-05 22:50
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, development]
---
<span style="font-family:arial;">Working with threads and counters can because a really pain if you do not take care. Fortunetly for us, .Net supplies us with a couple of methods that can make our life a little bit easier. The class <span style="font-family:courier new;">Interlocked</span>, which can be found in <span style="font-family:courier new;">System.Thread </span>allows you to increment, decrement, and set the value of an int or long as an atomic action, thus preventing race conditions where multiple threads update the same counter at the same time. The first methods are of course, <span style="font-family:courier new;">Increment() </span>and <span style="font-family:courier new;">Decrement()</span>, both accepting an int or long variable and update it as requested. The next method is the <span style="font-family:courier new;">Exchange()</span>, which allow you to set the value of either an int, a long or object variable, by passing it the variable by reference, and the next requested value. Another interesting method in the <span style="font-family:courier new;">CompareExchange()</span>. This method checks if the value of the variable is as expected, and only then update it. The methos returns the current value of the variable, whether it was updated or not, allowing you to redo you calculations if the value changed while you were calculating the new value. Below is an example take from </span>[<span style="font-family:arial;">MSDN</span>](http://msdn.microsoft.com/library/en-us/cpref/html/frlrfsystemthreadinginterlockedclasscompareexchangetopic.asp)<span style="font-family:arial;"><span style="font-family:arial;">:</span> </span>

``` c#:html:nocontrols:nogutter
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
```



