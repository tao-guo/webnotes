---
layout: default
title: web learning notes
---

<ul>
{% for page in site.pages where %}
	{% if page.url != "/" %}
		<li><a href="/{{ site.baseurl }}{{ page.url }}">{{ page.url }}</a></li>
	{% endif %}
{% endfor %}
</ul>
