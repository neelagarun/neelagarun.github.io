---
layout: page
title: Puzzles
permalink: /puzzles/
---

{% for post in site.posts %}
  {% if post.categories contains "puzles" %}
- [{{ post.title }}]({{ post.url }})
  {% endif %}
{% endfor %}
