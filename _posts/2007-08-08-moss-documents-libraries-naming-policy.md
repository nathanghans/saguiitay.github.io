---
layout: post
title: "MOSS Documents Libraries naming policy"
date: 2007-08-08 12:33
author: saguiitay
comments: true
categories: [Development]
tags: [SharePoint]
share: true
---
When you create a new Documents Library in MOSS, you are asked to provide a title for that Library. 
The provided title will be later used to define the internal name of the Library, as well as its URL. 
This is where the problems start. It only makes sense that MOSS allows the user to enter any value for the Library's title. 
However, there are a lot of limitations on internal names, and even more on URLs. 
This result in MOSS doing some kind of encoding/validation on the title. 
The result might range from having the name exactly the same as the title, to the name being completely different and unrelated. 
After some digging, I came up with the following logic: 

{% highlight text %}

    For each char:
      if char is valid
        add it to URL
      if char is invalid
        if URL already ends with '_' (underscore)
          don't do anything
        else
          add '_' (underscore)

    Strip '_' from beginning and end of URL
    If URL is empty
		replace URL with a legal DocLibXXX value where XXX is a number
	
{% endhighlight %}

	
Invalid characters are the following:

1. Any character bellow 0x20
2. Any of the following characters: ~!@\#$%^&\*{}[]-=+\\:"',?/.
3. Any character that takes more than 1 byte (Unicode and such)



