---
layout: page
title: Puzzles
permalink: /puzzles/
---

{% for post in site.posts %}
  {% if post.categories contains "puzzles" %}
- [{{ post.title }}]({{ post.url }})
  {% endif %}
{% endfor %}
