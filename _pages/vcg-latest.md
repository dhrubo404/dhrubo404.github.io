---
title: "VCG — Writeups"
permalink: /reports/vcg-latest/
---

{% assign writeups = site.pages | where_exp: "p", "p.path contains '_pages/writeups/'" | sort: 'title' | reverse %}

{% for post in writeups %}
- [{{ post.title }}]({{ post.permalink }})
{% endfor %}
