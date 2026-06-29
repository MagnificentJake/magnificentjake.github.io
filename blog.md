---
layout: page
title: Blog
permalink: /blog/
---

<ul style="list-style: none;">
  {% for post in site.posts %}
    <li>
      <h3><time datetime="{{ post.date | date: "%Y-%m-%d" }}">{{ post.date | date: "%B %-d, %Y" }}</time>
      &raquo; 
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>