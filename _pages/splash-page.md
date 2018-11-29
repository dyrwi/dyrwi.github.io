---
title: "WebAppHack"
layout: splash
permalink: /
date: 2018-10-01
header:
  overlay_color: "#000"
  overlay_filter: "0.5"
  overlay_image: /assets/images/unsplash-image-1.jpg
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
excerpt: "I am Ben Wilson. Blogging about web dev, pen testing and general musings..."
---

{% assign posts = site.posts %}
{% for post in posts %}
  {% include index-post.html %}
{% endfor %}