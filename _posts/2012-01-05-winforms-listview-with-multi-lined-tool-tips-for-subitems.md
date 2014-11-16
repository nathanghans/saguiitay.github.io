---
layout: post
title: "WinForms: ListView with Multi-lined tool-tips for SubItems"
date: 2012-01-05 16:06
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, WinForms, ListView]
share: true
redirect_from:
 - /2012-01-05-winforms-listview-with-multi-lined-tool-tips-for-subitems/
 - /development/winforms-listview-with-multi-lined-tool-tips-for-subitems/
---
If you've worked with ListViews in WinForms before, you might have encountered the discouraging fact that the default ListView does 
not support multi-lined tool-tips for SubItems. For this to work, you have to create a custom control that inherits from ListView, 
and make some nasty calls to P/Invoke. 

Here's how this can be achieved: 

{% highlight csharp %}
public class ListViewEx : ListView 
{ 
	private const int WM_NOTIFY = 78;
	private const int TTN_FIRST = -520;
	private const int TTN_NEEDTEXT = (TTN_FIRST - 10);
	
	[StructLayout(LayoutKind.Sequential, CharSet = CharSet.Auto)]
	public struct NMHDR 
	{
		public IntPtr hwndFrom;
		public Int32 idFrom;
		public Int32 code;
	}
	
	[StructLayout(LayoutKind.Sequential, CharSet = CharSet.Auto)]
	public struct NMTTDISPINFO
	{
		public NMHDR hdr;
		[MarshalAs(UnmanagedType.LPTStr)]
		public string lpszText;
		[MarshalAs(UnmanagedType.ByValTStr, SizeConst = 80)]
		public string szText;
		public IntPtr hinst;
		public int uFlags;
		public IntPtr lParam;
	}
	
	[DllImport("user32.dll", CharSet = CharSet.Auto)]
	public static extern IntPtr SendMessage(IntPtr hWnd, IntPtr uMsg, IntPtr wParam, IntPtr lParam); 
	
	protected override void OnNotifyMessage(Message m)
	{
		// Handle only WM_NOTIFY messages 
		if (m.Msg != (int)WM_NOTIFY)
		{
			base.OnNotifyMessage(m);
			return;
		}
		
		// Get the details of the WM_NOTIFY message, in the form of an NMHDR object
		var nmHdr = (NMHDR) Marshal.PtrToStructure(m.LParam, typeof (NMHDR));
		
		// We will handle only the TTN_NEEDTEXT message 
		if (nmHdr.code != TTN_NEEDTEXT)
			return;
		
		// Calculate the tool-tip 
		var tip = CalculateToolTip();
		if (tip == null)
			return;
		
		// Get the details of the notification message in the form of an NMTTDISPINFO object
		var nmttDispInfo = (NMTTDISPINFO)Marshal.PtrToStructure(m.LParam, typeof(NMTTDISPINFO));
		
		// Set max-width, to force multi-line tooltips (here, I've hard-coded the max-width to 320px.
		// You can calculated it based on the length of the tool-tip, screen resolution, etc.)
		SendMessage(nmttDispInfo.hdr.hwndFrom, (IntPtr)1048, IntPtr.Zero, (IntPtr)320);
		
		// Update tool-tip text
		nmttDispInfo.lpszText = tip;
		
		// Update the handle of the object that will host the tool-tip
		nmttDispInfo.hdr.hwndFrom = m.HWnd;
		
		// Update message information with new data
		Marshal.StructureToPtr(nmttDispInfo, m.LParam, false);
	}
	
	private string CalculateToolTip()
	{
		// Convert MousePosition to position inside the ListView
		var pt = PointToClient(MousePosition);
		// Perform HitTest, to allow us to find the correct ListView item/subitem
		var hitTestInfo = HitTest(pt);
		var item = hitTestInfo.Item;
		var subitem = hitTestInfo.SubItem;
		
		// If we didn't hit any item, no need for a tool-tip
		if (item == null)
			return null;
		
		return string.Format("Tool-tip for item: {0}\r\nSubitem: {1}", item.Index, item.SubItems.IndexOf(subitem));
	}
}
{% endhighlight %}

As you can see, the code is not overly complex, but not something that you could have guessed without ~~Googling~~ Binging it first.
