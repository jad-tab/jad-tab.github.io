---

title: "Certifications"
custom_css: certifications
permalink: /certifications/
header: 
 image: "/images/banner.jpg"
---

## List of certifications
{% for album in site.data.certifications %}
  <article>
    <a href="{{ album.url }}">
      <img src="{{ album.img }}" alt="{{ album.title }} {{ album.platform }}"/> 
      <p>{{ album.title }}
    on {{ album.platform }}</p></a>
	{{ album.description }}
    {% if release-date %}
      <span class="release-date">{{ album.release_date | date: "%b %-d, %Y" }}</span>
    {% endif %}
  </article>
{% endfor %}
 