---
title: "SCSS"
layout: archive
permalink: categories/scss
author_profile: true
sidebar_main: true
---



{% assign posts = site.categories.SCSS %}
<p class="notice--info">
  <strong>SCSS 강의 메모</strong> <br> 
  <span>온라인 강의를 들으면서 필요한 부분만 메모한 포스트입니다.</span> <br>
  <span>References: <a href="https://heropy.blog/2018/01/31/sass/">https://heropy.blog/2018/01/31/sass/</a></span>
</p>
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
