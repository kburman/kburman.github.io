---
layout: post
title: "Operating system from scratch"
---

{% for post in site.categories.os-dev %}
  <h4>
    <span>
    	#{{ post.chapter }} 
    </span>
    <a href="{{ post.url }}">
      {{ post.title }}
    </a>
  </h4>
{% endfor %}