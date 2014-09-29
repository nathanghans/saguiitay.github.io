---
layout: post
title: "Creating a user in Documentum"
date: 2007-08-02 18:38
author: saguiitay
comments: true
categories: [Development, Documentum]
tags: [Documentum, EMC]
---
<span style="font-family:arial;">Here's 2 ways to create a user in Documentum: Using the DQL:</span>

        create dm_cabinet object
        set object_name = '%USERNAME%'
        go

        create dm_user object
        set user_name = '%USER DISPLAY NAME%,
        set default_folder = '/%USERNAME%',
        set user_os_name = '%USERNAME%',
        set user_privileges = %PRIVILEGES%',
        set user_address = '%USER EMAIL%
        go

<span style="font-family:arial;">Using the IAPI: </span>

        create,c,dm_user
        set,c,l,user_name
        set,c,l,user_os_name
        set,c,l,user_address
        save,c,l



