---
layout: apps-index
permalink: /windows-phone/
title: "Windows Phone"
date: 2013-06-14 13:00
author: saguiitay
categories: []
tags: []
---
As part of my work, I've developed several apps and games for Microsoft's [Windows Phone](http://www.windowsphone.com) platform:


{% for app_hash in site.data.wp_apps %}
{% assign app = app_hash[1] %}
<article class="hentry">
  <header>
      <h1 class="entry-title">
		{% if app.small_tile %}
		  <a href="{{ site.url }}{{ app.page }}" rel="bookmark" title="{{ app.name }}"><img src="{{site.url}}{{app.small_tile}}" /></a> <a href="{{ site.url }}{{ app.page }}" rel="bookmark" title="{{ app.name }}">{{ app.name }}</a>
		{% else %}
		  <a href="{{ site.url }}{{ app.page }}" rel="bookmark" title="{{ app.name }}">{{ app.name }}</a>
		{% endif %}
	  </h1>
  </header>
  <div class="entry-content">
	<div>
      {{ app.description }}
	</div>
	{% if app.download_link %}
	  <div class="pull-right">
	    <a href="{{ app.download_link }}"><img src="{{ site.url }}/images/download-en-med2.png" /></a>
	  </div>
	{% endif %}
  </div><!-- /.entry-content -->
  <footer>
	{% if app.page %}
	  <a href="{{site.url}}{{app.page}}" rel="bookmark" title="{{ app.name }}">Read more...</a>
	{% endif %}
  </footer>
</article><!-- /.hentry -->
{% endfor %}
