---
title: "Window/Linux 설정하기"
layout: archive
permalink: categories/os
author_profile: true
sidebar_main: true
---

<!-- 공백이 포함되어 있는 카테고리 이름의 경우 site.categories['a b c'] 이런식으로! -->

***

{% assign oss = site.categories.os %} {% for post in oss %} {% include archive-single.html type=page.entries_layout %} {% endfor %}