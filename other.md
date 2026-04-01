---
layout: page
title: Other
permalink: /other/
---

{% for post in site.posts %}
  {% if post.categories contains "other" %}
- [{{ post.title }}]({{ post.url }})
  {% endif %}
{% endfor %}
