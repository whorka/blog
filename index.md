---
layout: default
title: whorka.github.com/blog
---

## Articles

<ul>
{% for post in site.posts reversed %}
{% comment %} (stolen from pdurbin.github.com and added baseurl) {% comment %}
<li>{{ post.date | date:"%Y-%m-%d" }}: <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>

