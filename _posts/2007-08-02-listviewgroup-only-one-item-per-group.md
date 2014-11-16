---
layout: post
title: "ListViewGroup - only one item per group"
date: 2007-08-02 08:35
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, GUI]
share: true
redirect_from:
 - /2007-08-02-listviewgroup-only-one-item-per-group/
 - /development/listviewgroup-only-one-item-per-group/
---
As I was working on a new GUI part of the software I'm working on, I needed to allow the user to choose only a single item in each category. I overloaded the [ListView](http://msdn2.microsoft.com/en-us/library/system.windows.forms.listview.aspx)control, and override the [OnItemCheck](http://msdn2.microsoft.com/en-us/library/system.windows.forms.listview.onitemcheck.aspx)method. Here's the result:

{% highlight csharp %}
public class SingleItemPerGroupListView : ListView
{
    public SingleItemPerGroupListView()
    { }

    protected override void OnItemCheck(ItemCheckEventArgs ice)
    {
        // Always behave regulary when user uncheck an item
        if (ice.NewValue == CheckState.Unchecked)
        {
            base.OnItemCheck(ice);
            return;
        }

        // Get the checked item
        ListViewItem lvi = this.Items[ice.Index];

        // If no item has no group, handle regulary
        if (lvi.Group == null)
        {
            base.OnItemCheck(ice);
            return;
        }

        // If another item is checked, uncheck item
        foreach (ListViewItem otherLvi in lvi.Group.Items)
        {
            if (otherLvi == lvi)
                continue;

            if (otherLvi.Checked)
            {
                otherLvi.Checked = false;
            }
        }
    }
}
{% endhighlight %}




