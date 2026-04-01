---
layout: page
title: Travel
permalink: /travel/
---

{% for post in site.posts %}
  {% if post.categories contains "travel" %}
- [{{ post.title }}]({{ post.url }})
  {% endif %}
{% endfor %}
