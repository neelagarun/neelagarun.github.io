---
layout: page
title: Statistics
permalink: /statistics/
---

{% for post in site.posts %}
  {% if post.categories contains "statistics" %}
- [{{ post.title }}]({{ post.url }})
  {% endif %}
{% endfor %}
