---
layout: default
title: News
order: 10
---

{% assign items = site.data.news | sort: 'Date' | reverse %}

{% for item in items %}
##### {{ item.Date | date: '%m/%d/%Y' }} - [{{ item.Title }}](/assets/{{item.File}})
{% endfor %}