---
layout: page
title: Projects
---

{% for project in site.projects %}
  <h3>
    <a href="{{ project.url }}">
      {{ project.title }}
    </a>
  </h3>
{% endfor %}