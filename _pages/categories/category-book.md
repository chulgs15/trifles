---
title: "지식이 쌓이길 바라며..."
layout: archive
permalink: categories/book
author_profile: true
sidebar_main: true
---

<!-- 공백이 포함되어 있는 카테고리 이름의 경우 site.categories['a b c'] 이런식으로! -->

***

{% assign books = site.categories.book %}
{% for post in books %} {% include archive-single.html type=page.entries_layout %} {% endfor %}