---
layout: page
title: "Trivia Games"
date: 2013-06-14 17:45
author: saguiitay
comments: true
categories: []
tags: []
---
The trivia engine is based on the concept of an objects graph - a network of objects and the relations between them - similar to those used in social websites (such as FaceBook, and other social networks):

Each trivia game consists of a database of objects and the relations between these objects. Objects represent real-world objects, people, or even just abstract concepts. Relations are used to connect the objects, and describe how they are related to one another. For example, [The Beatles Trivia](http://www.wp7trivia.com/thebeatles/) consists of the following types of objects and relations:


#### Objects

-   Person - Paul, John, George and Ringo
-   Album - Help!, Let It Be, etc.
-   Song - Help!, She Loves Me, etc.
-   Lyrics - several phrases from each song
-   Year - 1963, 1964, etc.
-   Quote - A phrase said by one of The Beatles members
-   Event - Birth, death, etc

#### Relations

-   Album contains Song
-   Song is part of Album
-   Album was releasead in Year
-   Event happened to Person
-   Person is lead singer in Song
-   Lyrics appears in Song
-   Song contains Lyrics
-   Person said Quote
-   Quote was said by Person
-   Song is sung by Person

Based on these objects and relations, we can navigate the information in a meaningful way. Furthermore, we can start ask questions about our information. For example, based on the "Album contains Song" relation, we can ask "Which songs are part of the album *Let It Be*?", or even "Which songs are not part of the album *Please Please Me*"? Now that we have the objects graph, and we have a better understanding on what kind of information we can get from it, we are left with the task of defining the questions we would like our trivia to include. Continuing with The Beatles trivia, here are a few sample questions we can include in our game:
-   Who said a certain quote?
-   What year was a person born?
-   From a given list of albums, which of them contain a certain song?
-   From a given list of lyrics, which is not part of a certain song?

After deciding which questions we are interested in, all that remains is to query our database of objects, and navigate our relations, in order to find a single correct answer, and some wrong answers. For example, let's take a look at the third question above - *From a given list of albums, which of them contain a certain song?*. In order to generate it, we'll following the steps below:
1.  Choose a random album
2.  Retrieve all songs of the album, by following the *Album contains Song* relation
3.  Choose one of the songs in random
4.  Choose 3 songs in random, but make sure the don't appear in the list of songs of the selected album

Now that we have a question, a correct answer, and 3 incorrect answers, we can display all the information to the user, and let him answer, as seen in the image to the right.



