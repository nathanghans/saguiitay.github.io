---
layout: post
title: "Unbound items in a bound ComboBox"
date: 2007-09-06 09:17
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, GUI]
share: true
redirect_from:
 - /2007/09/06/unbound-items-in-a-bound-combobox/
 - /2007-09-06-unbound-items-in-a-bound-combobox/
 - /development/unbound-items-in-a-bound-combobox/
---

# Introduction

It is sometimes necessary to add items to a bound ComboBox that will appear at the beginning of the drop-down list. 
If you ever tried to add items to a `ComboBox` that was bound, you probably know that an `ArgumentException` is thrown, 
saying that "Cannot modify the Items collection when the DataSource property is set".

Unfortunately for UI programmers, it is required to add items, such as "Create new item..." at the beginning of the list.

# The DataSource

In order to permit such functionality, we are going to have to wrap our original `DataSource` with a class that allows us to add those extra items. 
The first thing you have to notice is that DataSource, as accepted by the `ComboBox`, must implement the `IList` interface.
Also, in order to display all the items, we'll have to implement the `IEnumerable` interface.

{% highlight csharp %}
public class UnboundWrapper : IEnumerable, IList
{
    public UnboundWrapper(IList dataSource)
    {
        _dataSource = dataSource;
        _extra = new ArrayList();
    }

    IList _dataSource; 
    ArrayList _extra;

    #region ExtraData
    public void AddExtraData(object extraItem)
    {
        _extra.Add(extraItem);
    }

    public void RemoveExtraData(object extraItem)
    {
        _extra.Remove(extraItem);
    }
    #endregion
}
{% endhighlight %}

The above code is just our basic skeleton, and shouldn't raise any question. 
Next, we'll implement the `IList` and `IEnumerable` methods. The main idea here is that we're supposed to handle an extra item, 
and we'll take it from the `_extra` ArrayList. Otherwise, we just pass the calls along to the contained `IList`, 
with a shift of the indices, that is the size of the extra items.

{% highlight csharp %}
    #region IList Members

    public bool IsReadOnly
    {
        get
        {
            return _dataSource.IsReadOnly;
        }
    }

    public object this[int index]
    {
        get
        {
            string val;
            if (index < _extra.Count)
                val = _extra[index].ToString();
            else
                val = _dataSource[index - 
                      _extra.Count].ToString();
            return val;
        }
        set
        {
            _dataSource[index - _extra.Count] = value;
        }
    }

    public void RemoveAt(int index)
    {
        if (index < _extra.Count)
            throw new InvalidOperationException();
        _dataSource.RemoveAt(index - _extra.Count);
    }

    public void Insert(int index, object value)
    {
        if (index < _extra.Count)
            throw new InvalidOperationException();
        _dataSource.Insert(index - _extra.Count, value);
    }

    public void Remove(object value)
    {
        _dataSource.Remove(value);
    }

    public bool Contains(object value)
    {
        return _dataSource.Contains(value);;
    }

    public void Clear()
    {
        _dataSource.Clear();
    }

    public int IndexOf(object value)
    {
        if (_extra.Contains(value))
            return _extra.IndexOf(value);
        return _dataSource.IndexOf(value) + _extra.Count;
    }

    public int Add(object value)
    {
        return _dataSource.Add(value);
    }

    public bool IsFixedSize
    {
        get
        {
            return _dataSource.IsFixedSize;
        }
    }

    #endregion

    #region ICollection Members

    public bool IsSynchronized
    {
        get
        {
            return _dataSource.IsSynchronized;
        }
    }

    public int Count
    {
        get
        {
            return _dataSource.Count + _extra.Count;
        }
    }

    public void CopyTo(Array array, int index)
    {
        _extra.CopyTo(array, index);
        _dataSource.CopyTo(array, index + _extra.Count);
    }

    public object SyncRoot
    {
        get
        {
            return _dataSource.SyncRoot;
        }
    }

    #endregion

    #region IEnumerable Members

    public IEnumerator GetEnumerator()
    {
        return new UnboundWrapperEnumerator(this);
    }

    #endregion
{% endhighlight %}

You'll notice that we've also implemented the `ICollection` interface. This is because `IList` inherits from it, leaving us with no choice.

Since we've implemented the `IEnumerable` interface, we must supply the user with an enumerator. 
However, we cannot supply the `IList`'s default enumerator, since it won't include the extra items. 
So now, we have to create a new class - an enumerator:

{% highlight csharp %}
class UnboundWrapperEnumerator : IEnumerator
{
    public UnboundWrapperEnumerator(UnboundWrapper wrapper)
    {
        _wrapper = wrapper;
    }

    private UnboundWrapper _wrapper;
    int _index = -1;
        
    public void Reset()
    {
        _index = -1;
    }

    public object Current
    {
        get
        {
            return _wrapper[_index];
        }
    }

    public bool MoveNext()
    {
        if (_index >= _wrapper.Count - 1)
            return false;

        _index++;
        return true;
    }
}
{% endhighlight %}

As you can see, the enumerator is even simpler that the wrapper itself.

# Example

Before we leave, let's have a look at a simple example. 
Create a new Form, and add a ComboBox to it. In the constructor of the form, add the following code:

{% highlight csharp %}
string[] items = new string[] {"s1", "s2", "s3"};

UnboundWrapper w1 = new UnboundWrapper(items);
w1.AddExtraData("a");
w1.AddExtraData("b");
w1.AddExtraData("c");
w1.AddExtraData("z");

comboBox1.DataSource = w1;
{% endhighlight %}

In the above example, the DataSource is a simple string array. 
It shouldn't be too hard to expand the code to support more "interesting" data sources, such as a DataSet.
