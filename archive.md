---
layout: page
title: Archive
---

## Blog Posts

{% for post in site.posts %}
{% year = post.date | date: '%Y'}
{{year}}
   * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}

