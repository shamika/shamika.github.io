---
layout: page
title: Archive
---



{% for post in site.posts %}
  {% unless post.next %}
    <tr><th>{{ post.date | date: '%Y' }}</th><th>&nbsp;</th></tr>
  {% else %}
    {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
    {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
    {% if year != nyear %}
      <tr><th>{{ post.date | date: '%Y' }}</th><th>&nbsp;</th></tr>
    {% endif %}
  {% endunless %}
{% endfor %}
