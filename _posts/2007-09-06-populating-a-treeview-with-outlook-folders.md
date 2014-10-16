---
layout: post
title: "Populating a TreeView with Outlook folders"
date: 2007-09-06 19:03
author: saguiitay
comments: true
categories: [Development]
tags: [.Net, CDO, MAPI, Outlook]
share: true
redirect_from:
 - /2007/09/06/populating-a-treeview-with-outlook-folders/
---
In this post I will demonstrate how to populate a TreeView with the different Outlook folders. 
We'll be using the Outlook Object Model (OOM) to retrieve the folder and some basic information about them. 
The code itself is well documented, so I'll not explain it.

{% highlight csharp %}
private void GetFolders(MAPIFolder currentFolder)
{
    TreeNodeCollection nodes = null;
    if (currentFolder == null)
    {
        // If current folder is null, we'll be adding the root folders to
        // the TreeView itself
        nodes = treeView1.Nodes;
    }
    else
    {
        // otherwise, find th node that represent the MAPIFolder we're about
        // to expand
        TreeNode parentNode = folderToNode[currentFolder] as TreeNode;
        if (parentNode == null)
            throw new ArgumentException("currentFolder");
        nodes = parentNode.Nodes;
    }

    // If this folder was already expanded, there's nothing left for us to do
    if (currentFolder != null && folderExpanded[currentFolder] != null)
        return;
    else if (currentFolder != null)
    {
        // Otherwise, clear the children nodes of the node we're about to expand
        // This is done since we might have placed a Dummy node there - see later
        if (nodes.Count > 0)
            nodes.Clear();
        // Mark folder as expanded
        folderExpanded[currentFolder] = true;
    }
    
    // Retrieve the root/sub folders
    Folders folders = (currentFolder == null ? nameSpace.Folders : currentFolder.Folders);
    foreach (MAPIFolder folder in folders)
    {
        // If this folder already has a node, we're done with it
        if (folderToNode[folder] != null)
            continue;
        // Generate a node for the current subfolder
        TreeNode newNode = GenerateNode(folder);
        // Add folder to hashes and to it's parent node
        nodeToFolder[newNode] = folder;
        folderToNode[folder] = newNode;
        nodes.Add(newNode);
    }
}

private TreeNode GenerateNode(MAPIFolder folder)
{
    // Create a new node with the correct name
    TreeNode newNode = new TreeNode(folder.Name);
    // Check if and how the number of items should be displayed
    switch (folder.ShowItemCount)
    {
        case OlShowItemCount.olNoItemCount:
            // Don't display the number of items
            break;
        case OlShowItemCount.olShowTotalItemCount:
            // Display the total number of items
            if (folder.Items.Count > 0)
                newNode.Text += " (" + folder.Items.Count + ")";
            break ;
        case OlShowItemCount.olShowUnreadItemCount:
            // Display only the number of unread items
            if (folder.UnReadItemCount > 0)
                newNode.Text += " (" + folder.UnReadItemCount + ")";
            break;
    }

    // If folder has any subfolders, create a dummy node beneath it. This is
    // used so that the node will have the nice plus (+) sign next to it
    // so the user will know there are subfolders. This Dummy node will be
    // removed once the folder is actually expanded
    if (folder.Folders.Count > 0)
        newNode.Nodes.Add(new TreeNode(DummyNode));

    return newNode;
}

private const string DummyNode = "{1194595F-CAF7-474b-8972-CF143F097243}";
private Hashtable nodeToFolder = new Hashtable();
private Hashtable folderToNode = new Hashtable();
private Hashtable folderExpanded = new Hashtable();
{% endhighlight %}



