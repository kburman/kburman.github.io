---
layout: page
title: Projects
---

{% assign project_posts = site.posts | group_by: 'project_slug' %}
{% for project in project_posts %}
  {% if project.name == '' %}
    {% continue %}
  {% endif %}

  <h3>
    <a name="{{project.name}}" style="color: black; text-decoration: none;">{{ site.data.projects[project.name]['display_name'] }}</a>
  </h3>
  {% assign posts = project['items'] | sort: 'chapter'%}
  {% for post in posts %}
  <h4>
    <span>
    	#{{ post.chapter }} 
    </span>
    <a href="{{ post.url }}">
      {{ post.title }}
    </a>
  </h4>
  {% endfor %}
{% endfor %}
