---
title: "Windsor Factories and Obfuscation"
date: 2011-03-02 17:03
author: saguiitay
categories: [Development]
tags: [.Net, Obfuscation, Windsor]
redirect_from:
 - /2011-03-02-windsor-factories-and-obfuscation/
 - /development/windsor-factories-and-obfuscation/
---
I love [Windsor](http://stw.castleproject.org/Windsor.MainPage.ashx) - it's simplicity yet versatility amazes me every time I use it.

In my last project, I've used it, along with the [TypedFactoryFacility](http://stw.castleproject.org/Windsor.Typed-Factory-Facility.ashx) in 
order to "late-resolve" some objects. The resolved object required some parameters to be passed to the CTOR - nothing too complex - 
you just create an annonymous type with properties that match the names of the parameters, and Windsor handles the matching and passes the values. Piece of cake.

Everything worked perfectly fine for a while. That is, until we started to test our release setups. 

Our testing team started to complain that the UI is not updated when they click the various buttons.

After some digging, I figured that objects are not being created in the Business Logic layer that uses Windsor, which are then reflected in the UI.

It took me almost a day to figure things out - in our release setups, our code is obfuscated. The anonymous type mentioned earlier is compiler-generated, 
and then obfuscated. Since it's a private class, it's properties are renamed, which result in Windsor not being able to match properties to parameters.

Once that was clear, the solution was simple - I replaced the anonymous type with a regular class, and marked it as Serializable, 
which notifies the obfuscation not to touch it. After that, things went back to work - the class was not obfuscated, which means that the properties 
where not renamed. That in turn meant that Windsor was able to match properties to parameters, and the factory was able to resolve the objects.


