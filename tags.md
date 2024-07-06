---
layout: base
title: "Tags"
permalink: /tags/
---

<h1>Tags</h1>

<ul>
  {% for tag in site.tags %}
    <li><a href="#{{ tag[0] | slugify }}">{{ tag[0] }} ({{ tag[1] | size }})</a></li>
  {% endfor %}
</ul>

{% for tag in site.tags %}
  <h2 id="{{ tag[0] | slugify }}">- {{ tag[0] }}</h2>
  <ul>
    {% for post in tag[1] %}
        <li><a href="{% if site.github and site.github.repository_nwo %}{{ "/" | append: site.github.repository_name | append: post.url }}{% else %}{{ post.url }}{% endif %}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}
