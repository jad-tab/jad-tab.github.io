---
title: "Certifications"
permalink: /certifications/
header: 
 image: "/images/banner.jpg"
---


## Certifications

{% for certification in site.data.certifications %}
- [{{ certification.title }} by {{ certification.platform }}]({{ certification.url }})
{% endfor %}
