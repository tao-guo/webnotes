---
layout: default
title: web learning notes
---

<ul>
{% for page in site.pages where %}
	{% if page.url != "/" %}
		<li><a href="{{ page.url | prepend: site.baseurl }}">{{ page.url }}</a></li>
	{% endif %}
{% endfor %}
</ul>
