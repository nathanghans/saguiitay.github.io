---
layout: archive
title: "Games"
date: 2011-12-30 12:22
permalink: /games/
author_profile: false
feature_row:
  - image_path: unsplash-gallery-image-1-th.jpg
    alt: "placeholder image 1"
    title: "Placeholder 1"
    excerpt: "This is some sample content that goes here with **Markdown** formatting."
  - image_path: unsplash-gallery-image-2-th.jpg
    alt: "placeholder image 2"
    title: "Placeholder 2"
    excerpt: "This is some sample content that goes here with **Markdown** formatting."
    url: "#test-link"
    btn_label: "Read More"
    btn_class: "btn--inverse"
  - image_path: unsplash-gallery-image-3-th.jpg
    title: "Placeholder 3"
    excerpt: "This is some sample content that goes here with **Markdown** formatting."
---

{% include base_path %}

<div class="grid__wrapper">
  {% for post in site.games %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
