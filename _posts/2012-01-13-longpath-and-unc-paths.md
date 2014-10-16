---
layout: post
title: "LongPath and UNC paths"
date: 2012-01-13 20:00
author: saguiitay
comments: true
categories: [Development]
tags: [File System, Path, UNC]
share: true
redirect_from:
 - /2012/01/13/longpath-and-unc-paths/
 - /2012-01-13-longpath-and-unc-paths/
 - /2012-01-13-longpath-and-unc-paths
---

A colleague of mine started using the LongPath package provided as part of the [Base Class Libraries project in CodePlex](http://bcl.codeplex.com/).
This package seemed to be working just fine when trying to access local folders, both long and short. However, the provided code does not 
work with UNC paths - paths that are in the format of \\server\share\

A quick bing search brought me to the MSDN page named [Naming Files, Paths, and Namespaces](http://msdn.microsoft.com/en-us/library/windows/desktop/aa365247(v=vs.85).aspx)
that gives a lot of background on filenames, paths, and limitations, which includes the following section:

> The "\\?\" prefix can also be used with paths constructed according to the [universal naming convention](http://en.wikipedia.org/wiki/Path_%28computing%29) (UNC).
> To specify such a path using UNC, use the "\\?\UNC\" prefix. For example, "\\?\UNC\server\share", where "server" is the name of the computer and "share" is the
> name of the shared folder. These prefixes are not used as part of the path itself. They indicate that the path should be passed to the system with minimal
> modification, which means that you cannot use forward slashes to represent path separators, or a period to represent the current directory, or double dots to
> represent the parent directory. Because you cannot use the "\\?\" prefix with a relative path, relative paths are always limited to a total of **MAX\_PATH** characters.

Usually, handling long paths is done by adding a prefix - "\\?\" - to the path. Meaning, the path D:\LongFolderName will become "\\?\D:\LongFolderName"

Handling UNC paths is a little different - you need to add a different prefix - "\\?\UNC\" which means that "\\server\share\" becomes "\\?\UNC\server\share\"


