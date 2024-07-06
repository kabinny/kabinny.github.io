---
title: "아이폰도 웹푸시가 된다 - PWA 개발 참고 자료"
excerpt: "PWA 도입"

categories:
  - PWA
tags:
  - PWA-Push-iOS
toc: true
sidebar_main: true
---

> 아이폰에서 웹 푸시를 받기 위한 현재진행형 고민과 삽질  
> 앱은 못 만들지만 알림은 보내고 싶어~

틈틈히 메모해둔 걸 보면서 쓰고 있는데, 만감이 교차한다. 웹 푸시를 보내는 기술적인 방법은 검색하면 많이 잘 나와있기 때문에 그런 부분 보다는 실질적으로 마주친 문제들 위주로 기록할 것 같다.

잘 되는 방법은 하나지만, 안 되는 방법을 많이 알게 되었다.

## 개발 환경

- iOS 테스트 위주: 안드로이드 기기는 갖고 있지 않아서 직접 테스트는 거의 못했지만 대략적으로는 알고 있음.
- iOS 버전: 16.4 ~ 17 beta. 이 모든 문제와 고민의 시작은 2023년 7~9월의 iOS 시점임을 고려해야 한다. 한창 iOS 17 베타 테스트 중이었다.
- 프론트엔드 개발 시점: 내가 후론트개발자라서..
- 프로젝트 환경: Vue2 + webpack (큰 상관이 있을까 싶지만..)

## 왜 PWA를 시작..

드디어 아이폰도 웹푸시가 되기 때문이다.  
앱에서야 늘 푸시를 받고 있지만, 웹에서의 푸시를 사파리가 지원해주기 시작한 것이다.
푸시라는 것이 네이티브 앱을 만들어야 사용할 수 있어서 저 멀리 상관없는 영역인 줄 알았는데 이렇게 갑자기 다가온다고 생각하니 신기했다.

앱 개발을 하기 위해서는 많은 자원이 필요하고 유지보수까지 고려하면 일이 더 커지지만, 웹 개발의 영역에서 푸시알림을 해결할 수 있다면 정말 좋은 기회라고 생각한다. 마케팅 적인 요소에서는 문자 발송 비용을 아낄 수도 있다. (앱이 아닌 PWA로 완전히 대체 하기에는 아직 문제가 조금 있다)

앱의 모습을 하고 있다는 것은 강력한 바로가기 기능같기도 해서 더 사용자와 가까워지는 효과도 있을 것 같다. 그리고 무엇보다도 개발자로서 새로운 기술을 도입해볼 수 있는 훌륭한 기회이다.

## PWA 개발 참고 자료

### PWA 전반

- [초심자를 위한 PWA 기본 동영상 강의](https://www.youtube.com/playlist?list=PLlrxD0HtieHjqO1pNqScMngrV7oFro-TY)  
  마소가 PWA에 꽤 진심인 것 같다. 좋은 자료가 많다.
- [30일에 끝내는 PWA 문서](https://microsoft.github.io/win-student-devs/#/30DaysOfPWA/README)  
  전체적으로 간략한 흐름을 알 수 있다.
- [PWA를 배워보자 문서](https://web.dev/learn/pwa/)  
  궁금증이 해소되는 설명이 많고, 정리가 잘 되어 있다.
- [해피쿠 블로그 PWA 카테고리](https://www.happykoo.net/@happykoo/menus/69)  
  PWA를 처음부터 만들어 보는 내용. 서비스 워커, 캐시, 푸시 등 여러 내용이 참고하기 좋게 되어 있다. 굉장히 자주 보게 되었다.
- [마스크 가능한 아이콘 설정 방법](https://web.dev/i18n/ko/maskable-icon/)  
  Lighthouse 의 PWA 검사에서 매니페스트에 마스크 가능한 아이콘이 없어서 해당 항목만 빨갛게 뜰 때, 이 글을 따라서 아이콘을 추가하면 통과할 수 있다.

### 웹 푸시 알림 참고

- [웹 푸시 알림 포스팅](https://geundung.dev/114)  
  PWA 관련 책 저자의 블로그. 웹 푸시 알림에 대한 전체적인 흐름을 알 수 있다.

웹 푸시 관련은 그 외 다른 여러 블로그들을 참고했다.

### 서비스 워커 참고

- [서비스 워커에서 파일 캐시하기](https://gist.github.com/oilsinwater/ab49aebc080448062405f853124c42e8)  
  캐시 전략 별로 구현 방법이 정리되어 있다.
- [mdn 서비스워커 예시들](https://github.com/mdn/serviceworker-cookbook)  
  다양한 예시 참고하기 좋다.
- [서비스 워커와 통신하는 방법](https://web.dev/two-way-communication-guide/#using-browser-apis)  
  여러 방법이 깔끔하게 소개되어 있다.
- [서비스워커에서 오프라인 폴백 페이지](https://web.dev/offline-fallback-page/)  
  오프라인 페이지 제공하는 방법이 많은 참고가 되었다.
- [IndexedDB 간단 정리](https://pks2974.medium.com/indexeddb-간단-정리하기-ca9be4add614)  
  정리가 잘 되어 있다. 아직은 이해가 어려웠다..

### 기타

- [PWA 사이트 모음](https://appsco.pe/)  
  재밌고 신기한게 참 많다.

## PWA-Push-iOS

아무튼 이번에 개발한 내용 관련된 것은 이 태그 'PWA-Push-iOS' 달고 쓸 예정이다.  
결론을 스포하자면.  
아이폰, 약간의 애증.. 그래도 아이폰 좋아. 아이폰을 여전히 좋아할 수 있는 멘탈(?)