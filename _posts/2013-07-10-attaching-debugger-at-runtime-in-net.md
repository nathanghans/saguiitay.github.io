---
layout: post
title: "Attaching debugger at runtime in .Net"
date: 2013-07-10 14:04
author: saguiitay
comments: true
categories: [.Net, Development]
tags: [.Net, c#, development]
---
Whenever you write code that starts a new Process, your debugging experience is rather lacking - you're stuck outside of the debugger. The following extension method will attach the Visual Studio debugger to your process:
Code Snippet

1.  <span style="background:#ffffff;color:#0000ff;">using</span><span style="background:#ffffff;color:#000000;"> System;</span>
2.  <span style="background:#ffffff;color:#0000ff;">using</span><span style="background:#ffffff;color:#000000;"> System.Diagnostics;</span>
3.  <span style="background:#ffffff;color:#0000ff;">using</span><span style="background:#ffffff;color:#000000;"> System.Linq;</span>
4.  <span style="background:#ffffff;color:#0000ff;">using</span><span style="background:#ffffff;color:#000000;"> System.Runtime.InteropServices;</span>
5.  <span style="background:#ffffff;color:#0000ff;">using</span><span style="background:#ffffff;color:#000000;"> EnvDTE;</span>
6.  <span style="background:#ffffff;color:#0000ff;">using</span><span style="background:#ffffff;color:#2b91af;"> Process</span><span style="background:#ffffff;color:#000000;"> = EnvDTE.</span><span style="background:#ffffff;color:#2b91af;">Process</span><span style="background:#ffffff;color:#000000;">;</span>
7.  8.  <span style="background:#ffffff;color:#0000ff;">namespace</span><span style="background:#ffffff;color:#000000;"> Sample</span>
9.  <span style="background:#ffffff;color:#000000;">{</span>
10.     <span style="background:#ffffff;color:#808080;">///</span><span style="background:#ffffff;color:#808080;">\<summary\></span>
11.     <span style="background:#ffffff;color:#808080;">///</span><span style="background:#ffffff;color:#008000;"> Class ProcessExtender</span>
12.     <span style="background:#ffffff;color:#808080;">///</span><span style="background:#ffffff;color:#008000;"> Attach to other processes in order to debug.</span>
13.     <span style="background:#ffffff;color:#808080;">///</span><span style="background:#ffffff;color:#808080;">\</summary\></span>
14.     <span style="background:#ffffff;color:#0000ff;">public</span><span style="background:#ffffff;color:#0000ff;">static</span><span style="background:#ffffff;color:#0000ff;">class</span><span style="background:#ffffff;color:#2b91af;">ProcessExtender</span>
15.     <span style="background:#ffffff;color:#000000;">{</span>
16.         <span style="background:#ffffff;color:#0000ff;">const</span><span style="background:#ffffff;color:#0000ff;">string</span><span style="background:#ffffff;color:#000000;"> progId = </span><span style="background:#ffffff;color:#a31515;">@"VisualStudio.DTE.11.0"</span><span style="background:#ffffff;color:#000000;">;</span>
17.         <span style="background:#ffffff;color:#008000;">//const string progId = @"VisualStudio.DTE.9.0"; // Vs2008</span>
18. 19.         <span style="background:#ffffff;color:#0000ff;">\#region</span><span style="background:#ffffff;color:#000000;"> -- Public Methods --</span>
20. 21.         <span style="background:#ffffff;color:#808080;">///</span><span style="background:#ffffff;color:#808080;">\<summary\></span>
22.         <span style="background:#ffffff;color:#808080;">///</span><span style="background:#ffffff;color:#008000;"> Attaches Visual Studio (2010) to the specified process.</span>
23.         <span style="background:#ffffff;color:#808080;">///</span><span style="background:#ffffff;color:#808080;">\</summary\></span>
24.         <span style="background:#ffffff;color:#808080;">///</span><span style="background:#ffffff;color:#808080;">\<param name="process"\></span><span style="background:#ffffff;color:#008000;">The process.</span><span style="background:#ffffff;color:#808080;">\</param\></span>
25.         <span style="background:#ffffff;color:#0000ff;">public</span><span style="background:#ffffff;color:#0000ff;">static</span><span style="background:#ffffff;color:#0000ff;">void</span><span style="background:#ffffff;color:#000000;"> Attach(</span><span style="background:#ffffff;color:#0000ff;">this</span><span style="background:#ffffff;color:#000000;"> System.Diagnostics.</span><span style="background:#ffffff;color:#2b91af;">Process</span><span style="background:#ffffff;color:#000000;"> process)</span>
26.         <span style="background:#ffffff;color:#000000;">{</span>
27.             <span style="background:#ffffff;color:#008000;">// Reference visual studio core</span>
28.             <span style="background:#ffffff;color:#2b91af;">DTE</span><span style="background:#ffffff;color:#000000;"> dte;</span>
29.             <span style="background:#ffffff;color:#0000ff;">try</span>
30.             <span style="background:#ffffff;color:#000000;">{</span>
31.                 <span style="background:#ffffff;color:#000000;">dte = (</span><span style="background:#ffffff;color:#2b91af;">DTE</span><span style="background:#ffffff;color:#000000;">)</span><span style="background:#ffffff;color:#2b91af;">Marshal</span><span style="background:#ffffff;color:#000000;">.GetActiveObject(progId);</span>
32.             <span style="background:#ffffff;color:#000000;">}</span>
33.             <span style="background:#ffffff;color:#0000ff;">catch</span><span style="background:#ffffff;color:#000000;"> (</span><span style="background:#ffffff;color:#2b91af;">COMException</span><span style="background:#ffffff;color:#000000;">)</span>
34.             <span style="background:#ffffff;color:#000000;">{</span>
35.                 <span style="background:#ffffff;color:#2b91af;">Debug</span><span style="background:#ffffff;color:#000000;">.WriteLine(</span><span style="background:#ffffff;color:#2b91af;">String</span><span style="background:#ffffff;color:#000000;">.Format(</span><span style="background:#ffffff;color:#a31515;">@"Visual studio not found."</span><span style="background:#ffffff;color:#000000;">));</span>
36.                 <span style="background:#ffffff;color:#0000ff;">return</span><span style="background:#ffffff;color:#000000;">;</span>
37.             <span style="background:#ffffff;color:#000000;">}</span>
38. 39.             <span style="background:#ffffff;color:#008000;">// Try loop - visual studio may not respond the first time.</span>
40.             <span style="background:#ffffff;color:#0000ff;">int</span><span style="background:#ffffff;color:#000000;"> tryCount = 5;</span>
41.             <span style="background:#ffffff;color:#0000ff;">while</span><span style="background:#ffffff;color:#000000;"> (tryCount-- \> 0)</span>
42.             <span style="background:#ffffff;color:#000000;">{</span>
43.                 <span style="background:#ffffff;color:#0000ff;">try</span>
44.                 <span style="background:#ffffff;color:#000000;">{</span>
45.                     <span style="background:#ffffff;color:#2b91af;">Processes</span><span style="background:#ffffff;color:#000000;"> processes = dte.Debugger.LocalProcesses;</span>
46.                     <span style="background:#ffffff;color:#0000ff;">foreach</span><span style="background:#ffffff;color:#000000;"> (</span><span style="background:#ffffff;color:#2b91af;">Process</span><span style="background:#ffffff;color:#000000;"> proc </span><span style="background:#ffffff;color:#0000ff;">in</span><span style="background:#ffffff;color:#000000;"> processes.Cast\<</span><span style="background:#ffffff;color:#2b91af;">Process</span><span style="background:#ffffff;color:#000000;">\>().Where(</span>
47.                       <span style="background:#ffffff;color:#000000;">proc =\> proc.Name.IndexOf(process.ProcessName) != -1))</span>
48.                     <span style="background:#ffffff;color:#000000;">{</span>
49.                         <span style="background:#ffffff;color:#000000;">proc.Attach();</span>
50.                         <span style="background:#ffffff;color:#2b91af;">Debug</span><span style="background:#ffffff;color:#000000;">.WriteLine(</span><span style="background:#ffffff;color:#2b91af;">String</span><span style="background:#ffffff;color:#000000;">.Format(</span><span style="background:#ffffff;color:#a31515;">"Attatched to process {</span><span style="background:#ffffff;color:#3cb371;">0}</span><span style="background:#ffffff;color:#a31515;"> successfully."</span><span style="background:#ffffff;color:#000000;">, process.ProcessName));</span>
51.                         <span style="background:#ffffff;color:#0000ff;">break</span><span style="background:#ffffff;color:#000000;">;</span>
52.                     <span style="background:#ffffff;color:#000000;">}</span>
53.                     <span style="background:#ffffff;color:#0000ff;">break</span><span style="background:#ffffff;color:#000000;">;</span>
54.                 <span style="background:#ffffff;color:#000000;">}</span>
55.                 <span style="background:#ffffff;color:#0000ff;">catch</span><span style="background:#ffffff;color:#000000;"> (</span><span style="background:#ffffff;color:#2b91af;">COMException</span><span style="background:#ffffff;color:#000000;">)</span>
56.                 <span style="background:#ffffff;color:#000000;">{</span>
57.                     <span style="background:#ffffff;color:#000000;">System.Threading.</span><span style="background:#ffffff;color:#2b91af;">Thread</span><span style="background:#ffffff;color:#000000;">.Sleep(1000);</span>
58.                 <span style="background:#ffffff;color:#000000;">}</span>
59.             <span style="background:#ffffff;color:#000000;">}</span>
60.         <span style="background:#ffffff;color:#000000;">}</span>
61. 62.         <span style="background:#ffffff;color:#0000ff;">\#endregion</span>
63. 64.     <span style="background:#ffffff;color:#000000;">}</span>
65. <span style="background:#ffffff;color:#000000;">}</span>



