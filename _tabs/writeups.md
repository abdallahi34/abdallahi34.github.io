---
# the default layout is 'page'
icon: fas fa-pen-nib
order: 5
title: Writeups
permalink: /writeups/
---

Machine and CTF write-ups — published once the machine is retired or the event has ended.

{% assign writeups = site.posts | where_exp: "post", "post.categories contains 'writeup'" %}

<div class="row row-cols-1 g-3 mt-1">
{% for post in writeups %}
{% assign img_src = nil %}
{% if post.image %}
  {% if post.image.path %}
    {% if post.media_subpath %}
      {% assign img_src = post.media_subpath | append: '/' | append: post.image.path %}
    {% else %}
      {% assign img_src = post.image.path %}
    {% endif %}
  {% else %}
    {% assign img_src = post.image %}
  {% endif %}
{% endif %}
<div class="col">
  <a href="{{ post.url | relative_url }}" class="text-decoration-none">
    <div class="card h-100 shadow-sm">
      {% if img_src %}
      <img src="{{ img_src | relative_url }}" class="card-img-top" alt="{{ post.title }}">
      {% endif %}
      <div class="card-body">
        <h5 class="card-title">{{ post.title }}</h5>
        <p class="text-muted small mb-1">{{ post.date | date: "%b %-d, %Y" }}</p>
        <p class="card-text text-muted">{{ post.excerpt | strip_html | truncate: 160 }}</p>
      </div>
    </div>
  </a>
</div>
{% endfor %}
</div>

{% if writeups.size == 0 %}
<p class="text-muted">No write-ups published yet — check back soon.</p>
{% endif %}
