---
layout: post
title: "New website"
date: 2014-10-06 21:00
author: saguiitay
comments: true
categories: [Development]
tags: [.Net]
share: true
---
In case you haven't noticed, this is my new website. I've spent several long days updating everything, so I thought I'd share a bit of my experience.

# Hosting

I'm using [GitHub Pages](http://https://pages.github.com/) to host the website files.
This gives me a very comprehensive versioning system (I have a tendency to accidently delete files) in a familiar environment. The hosting is free,
and it supports [custom domains](https://help.github.com/articles/about-custom-domains-for-github-pages-sites/).

It also supports [Jekyll](http://jekyllrb.com/) out-of-the-box, which brings me to my next point

# Content Management

I'm using Jekyll to staticly generate this website. Jekyll is a simple, blog-aware static site generator, which makes it easy to work with templates,
headers and footers, etc.

I've installed Jekyll on my development machine, and after playing with it for a few hours, I found myself quite comfortable in its environment.
I've encountered a couple of minor glitches (probably related to the fact that I'm running it on Windows, which is [not officially
supported](http://jekyllrb.com/docs/windows/#installation)), but nothing I couldn't handle.

I've searched for a few Jekyll templates, and decided to settle on [HPSTR](http://mademistakes.com/articles/hpstr-jekyll-theme/) by
[Michael Rose](http://mademistakes.com), which gave me a lot of customizations out-of-the-box (such as support for Google, Bing, Facebook and Twitter)
as well as a very nice layout.

Jekyll uses Markdown (or Textile) and Liquid converters for the content. If you aren't familiar with Markdown, I suggest you stop right now, and
go read about it a bit - it's becoming a (controversial) standard these days - it's being used by web giants such as GitHub and StackOverflow.
Liquid is used as the template engine, which allows you to easily work with a page template, add headers and footers, etc.

# Migrating from WordPress

Since my blog was previously hosted in WordPress.com, I needed to migrate all my content (mainly posts and pages). Since I was using the hosted
solution (WordPress.com, and not the self-hosted/on-premises WordPress platform), I couldn't rely on plugins for that.

I've found a basic tool that generate post files based on the WordPress Export XML File - <https://github.com/theaob/wpXml2Jekyll>. The tool itself
works, but I had to make some modifications to it:

1. I wanted to export the images from my posts, so I've added support to process the HTML of Posts using [HtmlAgilityPack](http://htmlagilitypack.codeplex.com/)
2. I also wanted to convert my posts from HTML to Markdown. I've decided to use [Pandoc](http://johnmacfarlane.net/pandoc/) for that.

The conversion to Markdown didn't go as smoothly as I expected, and I had to spend a lot of time to go over the posts, and make sure they are all
well structured and formatter correctly.

# Pages

In addition to all the posts, I've reviewed and reorganized the pages - mainly the [Windows Phone]({{ site.url }}/windows-phone/) related pages,
to dynamically display the various apps I've created (using [Jekyll's Data feature](http://jekyllrb.com/docs/datafiles/)).

# Tools

In addition to the above mentioned tool, I've used [Travis-CI](https://travis-ci.org/) as a Continous Integration service that hooks into GitHub,
and shows you build process, including detailed errors. This proved invaluable, since GitHub Pages runs the Jekyll generator for you every time you
push changes to the repository, but doesn't provide detailed errors if things go wrong.

# Conclusion

I believe that the website looks much better now, it should be much faster to load (being statically generated, instead of on-the-fly by WordPress).
Also, having more control over the layout, structure and content mean I'll be able to do much more with it.

I hope you enjoy the new website.