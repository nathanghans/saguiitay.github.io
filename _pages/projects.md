---
layout: archive
title: "Projects"
permalink: /projects/
author_profile: false
---

{% include base_path %}

<div class="grid__wrapper">
  {% for post in site.projects %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>