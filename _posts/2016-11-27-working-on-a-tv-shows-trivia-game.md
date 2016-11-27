---
layout: post
title: "Woring on a TV shows trivia game"
date: 2016-11-27 22:56
author: saguiitay
categories: [Development]
tags: [.Net]
---

Following my trivia games, I've decided to start working on a TV shows trivia. The first step of creating a trivia game, is to make sure I have enough data, and that I can represent it in a way
that my engine can generate questions.

Representing the data for TV shows as nodes in a graph is relatively easy. I can create nodes for the following "objects":

  * Show - this represent a show, such as "Friends" or "Breaking Bad"
  * Characters - this will represent characters that appear in shows - Sheldon Cooper (from "The Big Bang Theory"), or Joey Tribbiani from "Friends" and "Joey"
  * Actors - the people playing the characters - Jim Parsons that plays Sheldon Cooper

These three are the obvious ones. Let's take a look at some more difficult objects:

  * Year - the year that a show premiered, or ended
  * Tagline - Most shows these days have a tagline - Gilmore Girls used "Best friends first. Mother and daughter second."
  * Quotes - Elementary's Sherlock Holmes saying "I expect nothing, which is why I am such an exceptional detective."

Of course, we can also add a few images-based objects:

  * Show Image
  * (optionally) Season Image
  * Character Image
  * Actor Image

Given those 9-10 objects, we can start working on the relationships between them (and for each relationship, it's inverted relationship):

  * Shows contain characters <--> Characters appear in shows
  * Shows have actors <--> Actors participate in shows
  * Actors play characters (in show) <--> Characters are played by actors (in show)
  * Show premiered in year <--> Year when show premiered
  * Show ended in year <--> Year when show ended
  * Show has tagline <--> Tagline belongs to show
  * Character said quote <--> Quote was said by character
  * Show/Character/Actor is depicted in image <--> Image depicts show/character/actor
  * ... and the list goes on...

Next post, I'll discuss where and how we can start gathering all the data for the game. 
