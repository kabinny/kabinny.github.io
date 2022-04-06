---
title: "블로그 만든 기록"
excerpt: "나도 할 수 있다, github.io 블로그 만들기"

categories:
 - Blog
tags:
 - Blog
 - github
 - jekyll
last_modified_at: 2021-05-13
toc: true
---

약 일 년 전 지금보다 더 아무것도 모르던 때, 깃헙 블로그 만들기를 시도했으나 비개발자에겐 난이도가 너무 어려웠다. 

일 년 동안 조금씩 공부를 했고, 이제 조금은 코드에 익숙해졌기 때문에 드디어 블로그를 만들 수 있었다. 적어도 인터넷에 검색하면 나오는 튜토리얼 중에 어떤 것이 내가 봐도 될 수준인 지 구별을 할 수 있다. 아직도 많이 헤매기는 하지만.  

## 블로그 생성에 jekyll, github pages 를 선택한 이유
1. 여러 블로그 플랫폼을 기웃거렸지만 정적 페이지 생성기를 이용한 블로그는 내가 포스트 원본(?)을 갖고 있다는 점이 매력적이었다. 
2. 조금 더 자유로운 커스텀이 가능하다. 
3. django로 블로그 같은거 만들고 있었는데 너무 어려워서 감당이 안됐다. 
4. 호스팅 비용을 딱히 쓰고 싶지 않았다. 
5. 지킬은 많이 쓰이기 때문에 루비의 루도 몰라도 친절한 자료가 많다. 
6. `username.github.io` 주소의 블로그 뭔가 멋져 보인다. ~~포스팅만 해도 잔디가 깔린다~~

## 블로그 만들면서 참고한 튜토리얼

### 블로그 기본 구축 하우투
[https://devinlife.com/howto/](https://devinlife.com/howto/)


아는 게 별로 없어도 따라하기 쉽게 설명이 되어 있다. 개인적으로 다른 사람에게 보여지는 목적은 없어서 검색 엔진 등록과 애드센스 부분은 건너뛰었다. 애널리틱스는 등록함. 

### 댓글 기능
[https://ansohxxn.github.io/blog/utterances/](https://ansohxxn.github.io/blog/utterances/)

다른 튜토리얼에도  disqus사용이 많아서 잘 몰랐는데 utterances라는 좋은 댓글 시스템이 있었다. 깔끔해서 마음에 들었다. 

### 이미지 호스트
[https://namhoon.kim/2020/07/02/topic/001/index.html](https://namhoon.kim/2020/07/02/topic/001/index.html)

깃허브 이슈를 이용한 방법은 관리가 애매할 거 같아서 구글 드라이브 사용하기로 마음먹었다. 포스트 내부 이미지를 전부  asset 폴더에 넣는 것도 용량적으로 불안하다. 

### 파비콘
[https://hongjuzzang.github.io/howto/favicon/](https://hongjuzzang.github.io/howto/favicon/)

파비콘도 달고 싶어서 참고할 예정. 

### 사이드바에 카테고리 표시하기
[https://ansohxxn.github.io/blog/category/#site-nav](https://ansohxxn.github.io/blog/category/#site-nav)

원하는 대로 보이려면 살짝 귀찮은 작업을 해야 하지만, 나중에 더 수정하면 마음에 들 것이다. 

--- 
쉽게 블로그를 만들 수 있게 지킬을 만들어 주시고, 
만드는 방법을 적어주신 많은 개발자님들께 감사하다.

나도 다른 누군가에게 도움이 되고 싶다. 
한참 나중의 일이겠지만. 

<img src="https://drive.google.com/uc?export=view&id=1IK7s-pUewzjLUxW0n5ITf06rKFpSC0zZ">
