---
layout: page
title: Steve's Posts
---
{% include JB/setup %}

This is a place where you might find my ramblings or code snippets which may or may not be useful to anyone including myself.

## Latest Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
