---
layout: post
title: "Bloomberg's Billionaires - Part 3: Getting images"
date: 2013-06-27 00:27
author: saguiitay
comments: true
categories: [Development, Windows Phone]
tags: [.Net, Billionaires, Windows Phone]
share: true
redirect_from:
 - /2013/06/27/bloombergs-billionaires-part-3-getting-images/
---
In the previous post, we've retrieved the information about the various billionaires. Now, let's take a look at how to get their images.
A single call to (http://www.bloomberg.com/billionaires/2/face-index-64963b5b944707b7efa7b2f1cf40d6eb.json) will bring us a pseudo-JSON,
which contains 2 values: FacePrints and Faces:

{% highlight text %}
    FACE_PRINTS=["9e45e1e449a316d0e28c838f024a70c0",...,"a54cc75ba87ee3e68f8db29e966077b4"];
    FACES={"carlos-slim":[0,0],"bill-gates":[0,1],...,"teh-hong-piow":[6,20]}
{% endhighlight %}

FacePrints contains a list of "references" to the images that we need to download. Faces, on the other hand, lists the billionaires,
and for each tells us which image to use (from the FacePrints list), and the index of the billionaire's face in that image.
Images are kept in a special format, which can be retrieved by appending the image Id from the FacePrints list to the following url: http://www.bloomberg.com/billionaires/db/portraits/chunk/2/

For example, (http://www.bloomberg.com/billionaires/db/portraits/chunk/2/9e45e1e449a316d0e28c838f024a70c0) will give us the following JSON:

{% highlight json %}
{
    "faces":"data:image/jpg;base64,/9j/4AAQS...9k=",
    "halos":"data:image/png;base64,iVBORw0KG...mCC",
    "masks":"data:image/gif;base64,R0lGODlhI...AA7"
}
{% endhighlight %}

As you can see, we're getting 3 images - faces, halos and masks, which are encoded into Base64. Decoding these 3 values will get us the
actual images. We can then take the correct part of the image for each billionaire, using the information we got in the Faces collection.

For example, for Teh Hong Piow, we got [6,20] which tells us to take the images with Id a54cc75ba87ee3e68f8db29e966077b4 (the 6th FacePrint),
and take the rectangle (0, 20*95, 100, 21*95) from the image, which gives us the following image: ![2]({{site.url}}/images/2.png)

Using the Halos image, we can get a nice halo to the face, and using the Masks image will give us the masking image, which will remove the black background.

Next time, we will see how to retrieve detailed information about each billionaire.
