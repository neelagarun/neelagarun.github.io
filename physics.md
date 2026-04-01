---
layout: page
title: Physics
permalink: /physics/
---

{% for post in site.posts %}
  {% if post.categories contains "physics" %}
- [{{ post.title }}]({{ post.url }})
  {% endif %}
{% endfor %}
