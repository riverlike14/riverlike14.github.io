---
title: "Algorithm"
layout: archive
permalink: /category/competitive-programming/
sidebar:
  nav: "sidebar-category"
---

{% assign posts = site.categories.competitive-programming %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
