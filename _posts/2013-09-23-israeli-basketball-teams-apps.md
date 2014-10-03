---
layout: post
title: "Israeli Basketball teams Apps"
date: 2013-09-23 23:24
author: saguiitay
comments: true
categories: [Development, Windows Phone]
tags: [Basketball, MAccabi Tel Aviv, Telerik, Windows Phone]
share: true
---
I've just released 2 news apps that focus on Israeli Basketball teams - [Maccabi Tel Aviv]({{site.url}}/windows-phone/sports-teams/maccabi-tel-aviv/ "Maccabi Tel Aviv - מכבי תל אביב") and [Hapoel Jerusalem]({{site.url}}/windows-phone/sports-teams/hapoel-jerusalem/ "Hapoel Jerusalem - הפועל ירושלים").

Both applications are quite simple, but feature a couple of points I wanted to try:

- The apps are in Hebrew (my native language) - this is the first time I write apps in Hebrew. The main challenge being that Hebrew is written right-to-left. In addition, Hebrew characters are Unicode, which requires special handling in a few places.
- Storing the data of the apps in [Dropbox](https://db.tt/ionax49) - by placing the data of the apps in a public [Dropbox](https://db.tt/ionax49) folder, I am able to update the data as needed, without needing to publish updates to the Marketplace.
- Using [Telerik Controls](http://www.telerik.com/products/windows-phone.aspx) proved to be extremely easy to use, and having the ability to easily localize the controls to Hebrews was priceless:
    - [JumpList](http://www.telerik.com/products/windows-phone/overview/all-controls/jumplist.aspx) & [DataBoundList](http://www.telerik.com/products/windows-phone/overview/all-controls/databoundlistbox.aspx) were used to display the list of players/coaches/management, as well as for the menu
    - [Calendar](http://www.telerik.com/products/windows-phone/overview/all-controls/calendar.aspx) was used for the games schedule
    - [BusyIndicator](http://www.telerik.com/products/windows-phone/overview/all-controls/busyindicator.aspx) shows an indicator when loading the the data
    - [Diagnostics](http://www.telerik.com/products/windows-phone/overview/all-controls/diagnostics.aspx) and [RateAppReminder](http://www.telerik.com/products/windows-phone/overview/all-controls/rateappreminder.aspx) components are also both used

All in all, these are quite simple apps that display information, such as players, coaches and the management team, as well as news and the games
schedule of the team. Although not extremely interesting technically, I feel it is important that the [Israeli marketplace](http://www.windowsphone.com/he-il/store)
gets more apps.