---
layout: post
title: "Writing an IsApplicationRunning method"
date: 2007-09-06 09:06
author: saguiitay
comments: true
categories: [Development]
tags: [.Net]
share: true
---

# Introduction

I never was a Win32 developer. But sometimes, the .NET classes just don't make the cut. 
During my work, I needed a way to check if a certain application is running in the background. 
Obviously, I turned to the `Process` class that can be found in the `System.Diagnostics` namespace. 
Not much long after, I had this perfect looking piece of code:

{% highlight csharp %}
if (Process.GetProcessesByName(appName).Length > 0)
{
    // Warn the user that the application
    // is running in the background
}
{% endhighlight %}

It took our QA team less than a minute to tear these one-and-a-half lines of code to pieces. 
It turns out, that the Process class in .NET relies heavily on the PerfMon process/application.
If the machine you're running has it disabled for some reason, you'll get an exception from the `GetProcessesByName` method.
And it will take the exception a couple of seconds to be thrown. 

And that's the best case scenario. A couple of minutes spent with the other developers taught me that the 
usage of Process might just freeze or process. This was of course unacceptable.

# The solution

I had no choice but to find another way. After some research, I found a couple of Win32 methods that I 
thought might help me: `Process32First` and `Process32Next`.

Now, I was ready to write my own IsProcessRunning method. Let's start with the different DllImport declarations:

{% highlight csharp %}
[DllImport("kernel32.dll")]
private static extern int Process32First(IntPtr hSnapshot, ref PROCESSENTRY32 lppe);

[DllImport("kernel32.dll")]
private static extern int Process32Next(IntPtr hSnapshot, ref PROCESSENTRY32 lppe);

[DllImport("kernel32.dll", SetLastError=true)]
private static extern IntPtr CreateToolhelp32Snapshot(uint dwFlags, uint th32ProcessID);

[DllImport("kernel32.dll", SetLastError=true)]
private static extern bool CloseHandle(IntPtr hSnapshot);

private const uint TH32CS_SNAPPROCESS = 0x00000002;

[StructLayout(LayoutKind.Sequential)]
private struct PROCESSENTRY32
{
    public uint dwSize;
    public uint cntUsage;
    public uint th32ProcessID;
    public IntPtr th32DefaultHeapID;
    public uint th32ModuleID;
    public uint cntThreads;
    public uint th32ParentProcessID;
    public int pcPriClassBase;
    public uint dwFlags;
    [MarshalAs(UnmanagedType.ByValTStr, SizeConst=260)] public string szExeFile;
}
{% endhighlight %}


And here are some basic explanations of the methods:
* `PROCESSENTRY32` - This struct will obviously be used to keep the information of the different processes in the system. 
* `Process32First`/`Process32Next` - These methods will serve to enumerate all the processes. 
* `CreateToolhelp32Snapshot` - We'll use this method to create a snapshot of the system and the processes. 

Now let's look at the code:

{% highlight csharp %}
public static bool IsProcessRunning(string applicationName)
{
    IntPtr handle = IntPtr.Zero;
    try
    {
        // Create snapshot of the processes
        handle = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
        PROCESSENTRY32 info = new PROCESSENTRY32();
        info.dwSize = (uint)System.Runtime.InteropServices.
                      Marshal.SizeOf(typeof(PROCESSENTRY32));

        // Get the first process
        int first = Process32First(handle, ref info);
        // If we failed to get the first process, throw an exception
        if (first == 0)
            throw new Tzunami.Common.TzunamiException("Cannot find first process.");

        // While there's another process, retrieve it
        do
        {
            if (string.Compare(info.szExeFile, 
                  applicationName, true) == 0)
            {
                return true;
            }
        }
        while (Process32Next(handle, ref info) != 0);
    }
    catch
    {
        throw;
    }
    finally
    {
        // Release handle of the snapshot
        CloseHandle(handle);
        handle = IntPtr.Zero;
    }
    return false;
}
{% endhighlight %}
