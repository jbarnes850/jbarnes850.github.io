---
layout: default
title: Blog
permalink: /blog/
---

# Blog

<div class="post-grid">
  {% for post in site.posts %}
  <article class="post-card">
    <div class="post-meta">
      <span class="post-date">{{ post.date | date: "%B %-d, %Y" }}</span>
      {% if post.categories %}
      <span class="post-categories">
        {% for category in post.categories %}
        <span class="category">{{ category }}</span>
        {% endfor %}
      </span>
      {% endif %}
    </div>
    <h3 class="post-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
    <div class="post-excerpt">{{ post.excerpt }}</div>
    <a href="{{ post.url | relative_url }}" class="read-more">Read more →</a>
  </article>
  {% endfor %}
</div>
