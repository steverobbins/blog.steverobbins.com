---
layout: page
title: Ramblings of a Web Dev
---
{% include JB/setup %}

This is a place where you might find my ramblings or code snippets which may or may not be useful to anyone including myself.

## About Me

I'm a (hat wearing) web developer who likes to innovate and experiment with new ideas.  My goal is to make the internet better.

I work as a software developer with [Kering](http://goo.gl/A5XNUe) at [Volcom](http://goo.gl/i6zjX9).

## Latest Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
