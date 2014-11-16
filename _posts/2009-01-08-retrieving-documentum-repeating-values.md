---
layout: post
title: "Retrieving Documentum repeating values"
date: 2009-01-08 16:45
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Documentum]
share: true
redirect_from:
 - /2009-01-08-retrieving-documentum-repeating-values/
 - /development/retrieving-documentum-repeating-values/
---
Documentum provides the functionality of "repeating" properties - properties that have more than one value. 
Retrieving those values is a simple matter of getting the number of values for that property, and then request each one of the values.

Here's a small utility method: 

{% highlight csharp %}
private static object[] GetRepeatingValue(IDfSysObject dfObj, string attributeName)
{
	int valuesCount = dfObj.getValueCount(attributeName);
	object[] values = new object[valuesCount];

	IDfValue val = null;
	for (int index = 0; index < valuesCount; index++)
	{
		try
		{
			val = dfObj.getRepeatingValue(attributeName, index);
			values[index] = val.asString();
		}
		finally
		{
			NAR(val);
			val = null;
		}
	}
	return values;
}
{% endhighlight %}
