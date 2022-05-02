---
title: "블로그 카테고리 수정하기"
excerpt: "사이드바의 카테고리 부분 수정"

categories:
  - Blog
tags:
  - Blog
  - jekyll
last_modified_at: 2022-05-02
toc: true
---

블로그 카테고리 수정은 자주하는 일이 아니지만 잊어버리기 쉬워서 기록해 두기로 한다.
사이드바에 카테고리 보이게 하는 방법은 이전 포스팅에서 적었지만 [이 포스팅](https://ansohxxn.github.io/blog/category/#site-nav)을 참고했다.

## 1. \_includes/nav-list-main 수정

원하는 서브타이틀 아래에 보이게 추가한다. 메인이나 포스팅 페이지에서 좌측에 보이는 사이드 부분이다.

```html
{% raw %}
<span class="nav__sub-title">etc</span>
<ul>
  {% for category in site.categories %} {% if category[0] == "Blog" %}
  <li><a href="/categories/blog" class="">Blog ({{category[1].size}})</a></li>
  {% endif %} {% if category[0] == "etc" %}
  <li><a href="/categories/etc" class="">etc ({{category[1].size}})</a></li>
  {% endif %} {% endfor %}
</ul>
{% endraw %}
```

## 2. \_pages/categories/category-카테고리이름.md 수정

카테고리의 메인 페이지이다. 카테고리별 설명도 추가할 수 있다.

```md
{% raw %}

---

title: "VUE"
layout: archive
permalink: categories/vue
author_profile: true
sidebar_main: true

---

{% assign posts = site.categories.Vue %}

<p class="notice--info">
  카테고리 하고 싶은 설명은 여기에...
</p>
{% for post in posts %} 
  {% include archive-single2.html type=page.entries_layout %} 
{% endfor %}
{% endraw %}
```

복잡하지는 않지만 대소문자 주의해서 구별해 써야 한다!
