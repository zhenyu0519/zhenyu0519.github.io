---
layout: page
title: About
description: The World Needs Programmers
keywords: Jeffrey, zhenyu
comments: true
menu: About
permalink: /about/
---

Jeffrey Is A Programmer

## Connect

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
