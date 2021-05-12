---
title: "SCSS"
layout: archive
permalink: categories/scss
author_profile: true
sidebar_main: true
---

<p class="notice--info">
  <strong>SCSS 강의 메모</strong> 
  <span>온라인 강의를 들으면서 필요한 부분만 메모한 포스트입니다.</span>
</p>

{% assign posts = site.categories.SCSS %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
