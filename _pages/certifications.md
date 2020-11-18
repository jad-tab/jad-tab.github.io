---
title: "Certifications"
permalink: /certifications/
header: 
 image: "/images/banner.jpg"
custom_css: certs.css
---


## Certifications
{% for album in site.data.certifications %}
  <article>
    <a href="{{ album.url }}">
      <img src="{{ album.img }}" alt="{{ album.title }} {{ album.platform }}"/>
      <p>{{ album.title }}</p>
    </a>
    <p>by {{ album.platform }}</p>
    {% if release-date %}
      <span class="release-date">{{ album.release_date | date: "%b %-d, %Y" }}</span>
    {% endif %}
  </article>
{% endfor %}

