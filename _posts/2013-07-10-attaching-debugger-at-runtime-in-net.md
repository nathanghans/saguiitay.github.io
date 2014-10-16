---
layout: post
title: "Attaching debugger at runtime in .Net"
date: 2013-07-10 14:04
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, Debugger]
share: true
redirect_from:
 - /2013/07/10/attaching-debugger-at-runtime-in-net/
---
Whenever you write code that starts a new Process, your debugging experience is rather lacking - you're stuck outside of the debugger.
The following extension method will attach the Visual Studio debugger to your process:

{% highlight csharp %}
using System;
using System.Diagnostics;
using System.Linq;
using System.Runtime.InteropServices;
using EnvDTE;
using Process = EnvDTE.Process;

namespace Sample
{
    ///<summary>
    /// Class ProcessExtender
    /// Attach to other processes in order to debug.
    ///</summary>
    publicstaticclassProcessExtender
    {
        conststring progId = @"VisualStudio.DTE.11.0";
    //const string progId = @"VisualStudio.DTE.9.0"; // Vs2008

    #region -- Public Methods --

    ///<summary>
    /// Attaches Visual Studio (2010) to the specified process.
    ///</summary>
    ///<param name="process">The process.</param>
    public static void Attach(this System.Diagnostics.Process process)
    {
        // Reference visual studio core
        DTE dte;
        try
        {
            dte = (DTE)Marshal.GetActiveObject(progId);
        }
        catch (COMException)
        {
            Debug.WriteLine(String.Format(@"Visual studio not found."));
            return;
        }

        // Try loop - visual studio may not respond the first time.
        int tryCount = 5;
            while (tryCount-- > 0)
            {
                try
                {
                    Processes processes = dte.Debugger.LocalProcesses;
                    foreach (Process proc in processes.Cast<Process>().Where(
                        proc => proc.Name.IndexOf(process.ProcessName) != -1))
                    {
                        proc.Attach();
                        Debug.WriteLine(String.Format("Attatched to process {0} successfully.", process.ProcessName));
                        break;
                    }
                    break;
                }
                catch (COMException)
                {
                    System.Threading.Thread.Sleep(1000);
                }
            }
        }
        #endregion
    }
}
{% endhighlight %}
