---
title: "서비스 워커 디버깅 방법"
excerpt: "그동안 했던 크로스 브라우징은 맛보기였다. 초 매운맛 사파리 디버깅"

categories:
  - PWA
tags:
  - PWA-Push-iOS
  - safari
  - service-worker
toc: true
sidebar_main: true
---

iOS.. 이렇게 까지 해야 하는 것인가. 앱 개발은 좀 나은 편인가?..

## PC - Chrome, AOS

개발자 도구에서 애플리케이션 탭을 연다.

### 애플리케이션

- 매니페스트: 매니페스트 파일의 내용이 잘 인식되는지 확인할 수 있다.
- Service Workers: 서비스 워커 상태(실행 중인지 등)를 알 수 있다.
- 저장용량: 서비스 워커에서 캐싱을 하면 그 용량을 볼 수 있다. 개발 중 서비스 워커를 수정하거나 변경사항이 있을 때는 여기서 ‘사이트 데이터 삭제’를 꼭 눌러야 한다.

### 저장용량

- IndexedDB: 서비스 워커에서 IndexedDB를 사용했다면 여기서 확인 가능하다.
- 캐시 저장공간: 캐싱할 때 설정한 캐시 이름별로 볼 수 있다. 캐시된 파일들 리스트를 쭉 볼 수 있어서 편리하다.

### 안드로이드

안드로이드는 PC에 연결해서 크롬의 개발자 도구를 그대로 사용해서 디버깅 할 수 있다.

## Mac OS - Safari

사파리의 개발자 도구는 PWA 를 들여다 보기에는 많이 열악하다.

앱 개발은 아니어서 Xcode를 사용하지는 않았다. (노트북에 Xcode가 깔리지 않았다.. 너무 오래된 맥북이라 그런거 같다. )

### 캐시 스토리지 확인

일단.. 바로 확인은 안되고 사파리 익스텐션을 설치해야 한다.

[Service Worker Detector](https://apps.apple.com/kr/app/service-worker-detector/id1530808337?mt=12)

이것으로 캐시 스토리지를 보는 게 조금은 편해졌지만, iOS 사파리에서는 사용할 수 없다.

### 콘솔창에서 확인

사파리 데브툴에는 크롬과 같은 ‘Application’ 이 없다.. 구글 선생님의 콘솔창으로 확인하기 비법을 정리해 본다.

[https://youtu.be/87RU7v6Y-bk](https://youtu.be/87RU7v6Y-bk)

**서비스 워커 전용 콘솔창을 찾아보자**

`상단 메뉴 > Develop > Service Workers > localhost(혹은 보고 있는 사이트)`

서비스 워커에서 보내는 콘솔 로그는 여기에서 확인가능하다. 크롬에서는 콘솔창 하나에 같이 나오는데 사파리는 서비스 워커의 콘솔이 분리되어 있다. 서비스 워커 콘솔창이 보인다면 서비스 워커가 설치되었다는 뜻이다.

**서비스 워커 등록 취소**

수정한 새 워커가 작동하기 위해 이전 워커 지우는 방법. 아래 코드를 서비스 워커 콘솔창에 직접 써서 실행하면 현재 등록된 모든 서비스 워커가 삭제된다.

```js
navigator.serviceWorker.getRegistrations().then((registrations) => {
  for (let registration of registrations) {
    registration.unregister().then((unregistered) => console.log(unregistered));
  }
});
```

**캐시 스토리지 확인**

콘솔에서 이렇게 쓰면 캐시 스토리지에 저장된 목록을 볼 수 있다. 너무 많으면 몇 초 정도 걸린다.

```js
caches.open("캐시 이름").then((cache) => {
  cache.keys().then((requests) => console.log(requests));
});
```

**캐시 스토리지에서 삭제**

```js
caches.open("캐시 이름").then((cache) => {
  cache.keys().then((keys) => {
    keys.forEach((request, index, array) => {
      cache.delete(request);
    });
  });
});
```

삭제 후 다시 캐시를 확인하면 빈 배열을 확인할 수 있다.

이렇게 써 두었지만 굉장히 불편하고 답답하기 때문에 개발 중에는 크롬으로 확인하려고 했다.

## iOS - Safari

맥에 usb로 연결하고 맥에서 사파리를 연다. 맥 사파리 개발자 메뉴에 연결된 기기가 뜨고 거기서 개발자 도구를 볼 수 있다. 서비스 워커 콘솔창도 거기서 열 수 있다. 사용 법은 맥 사파리 개발자 도구랑 같다. (매우 불편하다는 뜻)

그리고 PWA 앱도 사파리랑 같은 방법으로 확인할 수 있다. 불편한 점은, 한두가지가 아니지만, 앱을 종료할 때 열어두었던 개발자 도구와 콘솔창이 닫혀서 종료할 때 어떤 일이 일어나는지 파악하기 어렵다는 것이다. 마찬가지로 앱이 열릴 때를 확인하고 싶은데 앱이 열여 있지 않으면 개발자 도구나 콘솔창을 열 수 없기 때문에 앱이 열리는 시점에 어떤 일이 일어나는지 알기 어렵다.

## iOS - Chrome, Edge

이건 진짜 참고만 할 수 있고 왠만하면 iOS 관련 디버깅은 사파리로 할 수 밖에 없다. 사파리 외 브라우저에서 디버깅을 하고 싶다면 어쩔 수 없지만, 콘솔로그만 확인 가능하다.

크롬 브라우저 주소창에 `chrome://inspect` 를 입력해서 들어간다. `로깅 시작` 버튼을 누르고, 새 창을 열어 확인하고자 하는 사이트에 들어간다. 그리고 다시 `chrome://inspect`를 입력한 창으로 돌아오면 콘솔 로그가 찍혀 있는 것을 확인할 수 있다.

엣지 브라우저도 방법은 똑같고, `edge://inspect`로 들어가면 된다.
