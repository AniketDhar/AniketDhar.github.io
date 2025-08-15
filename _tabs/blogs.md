---
layout: page
title: "Blogs"
icon: fas fa-blog
order: 3
---

<ul class="post-list">
{% assign posts = site.posts | sort: 'date' | reverse %}
{% for post in posts %}
  <li class="mb-4">
    <h3 class="mb-1">
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </h3>
    <div class="text-muted mb-1">{{ post.date | date: "%b %d, %Y" }}</div>
    <p>{{ post.excerpt | strip_html | truncate: 180 }}</p>
  </li>
{% endfor %}
</ul>
