---
layout: post
title: "jQuery / Twitter Bootstrap Checkboxes List Tree Plugin"
date: 2012-12-21 10:33
author: saguiitay
comments: true
categories: [Development]
tags: [Checkboxes, JavaScript, JQuery, Twitter Bootstrap]
share: true
redirect_from:
 - /2012-12-21-jquery-twitter-bootstrap-checkboxes-list-tree-plugin/
 - /development/jquery-twitter-bootstrap-checkboxes-list-tree-plugin/
---
I've been using [jQuery](http://jquery.com/) and [Twitter Bootstrap](http://twitter.github.com/bootstrap/) quite a lot lately.
I've found myself in need of a more advanced control to display a list of check-boxes, grouped together by subject/area.
After some [binging](http://bing.com), and I've found this useful piece of code: <http://jsfiddle.net/clayzermk1/QD8Hs/>

I've had to made some modifications to it:

- The current implementation works only for a single instance of the control in the page
- I needed a somewhat different behavior for the check-boxes - selecting a group header should disable the "children" check-boxes
- I needed to have the ability to have a the title and the value of the check-box to be different
- I needed to be able to get only a list of selected check-boxes (instead of the complete list of parent-child check-boxes).

Here's the end result <http://jsfiddle.net/saguiitay/dAqaX/embedded/result/>

You can find the complete code here: <http://jsfiddle.net/saguiitay/dAqaX/>
