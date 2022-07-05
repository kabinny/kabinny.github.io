---
title: "WIL"
layout: archive
permalink: categories/wil
author_profile: true
sidebar_main: true
---



{% assign posts = site.categories.WIL %}

<p class="notice--info">
  <strong>Weekly I Learned</strong> <br> 
  <span>주간 공부 내용 정리한 포스트입니다. </span> 

</p>
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
