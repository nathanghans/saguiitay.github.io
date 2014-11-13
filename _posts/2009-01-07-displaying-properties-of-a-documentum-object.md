---
layout: post
title: "Displaying properties of a Documentum object"
date: 2009-01-07 23:09
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Documentum]
share: true
redirect_from:
 - /2009/01/07/displaying-properties-of-a-documentum-object/
 - /2009-01-07-displaying-properties-of-a-documentum-object/
 - /development/displaying-properties-of-a-documentum-object/
---
When working with Documentum TypedObjects, you almost always need to retrieve their properties. 
Below is a method to print those properties to the Console. Notice, that this example uses the `getAllRepeatingStrings()` method - 
a useful method for displaying values to the user, but not very useful if you need to process and work with the actual values. 

{% highlight csharp %}
public static void DisplayItem(IDfTypedObject obj)
{
	if (obj == null)
		return;
	Console.WriteLine("-------------------------------------------");
	int attrCount = obj.getAttrCount();
	for (int i = 0; i 
	{
		IDfAttr attr = null;
		try
		{
			attr = obj.getAttr(i);
			string attrName = attr.getName();
			Console.Write(attrName + ": ");
			if (!obj.hasAttr(attrName) obj.isNull(attrName))
			{
				Console.WriteLine("NULL");
				continue;
			}
			Console.WriteLine(obj.getAllRepeatingStrings(attrName, "; "));
		}
		finally
		{
			NAR(attr);
			attr = null;
		}
	}
}
{% endhighlight %}
