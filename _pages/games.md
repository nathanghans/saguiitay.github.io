---
layout: archive
title: "Games"
permalink: /games/
author_profile: true
---

{% include base_path %}

<div class="grid__wrapper">
  {% for post in site.games %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
