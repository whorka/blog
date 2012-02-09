---
layout: default
title: whorka.github.com/blog
---

## Articles

<ul>
{% // stolen from pdurbin.github.com and added baseurl %}
{% for post in site.posts reversed %}
<li>{{ post.date | date:"%Y-%m-%d" }}: <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>

