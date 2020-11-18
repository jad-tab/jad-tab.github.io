---
title: "Certifications"
permalink: /certifications/
header: 
 image: "/images/banner.jpg"
---




## Certifications

{% for album in site.data.certifications %}
- [{{ certifications.title }} by {{ certifications.platform }}]({{ certifications.url }})
{% endfor %}