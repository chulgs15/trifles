---
title: "blog 내용 정리"
layout: archive
permalink: categories/blog
author_profile: true
sidebar_main: true
---

<!-- 공백이 포함되어 있는 카테고리 이름의 경우 site.categories['a b c'] 이런식으로! -->

***

{% assign blogs = site.categories.blog %}
{% for post in blogs %} {% include archive-single.html type=page.entries_layout %} {% endfor %}