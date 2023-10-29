---
title: "manifest를 추가해서 설치 가능하게 만들기"
excerpt: "간단한 듯 아닌 듯 manifest 추가, 설치 방법"

categories:
  - PWA
tags:
  - PWA-Push-iOS
toc: true
sidebar_main: true
---

일단 manifest.json 파일을 추가해서 PWA로 설치할 수 있게 만들어야 했다. 푸시를 받기 위해서는 사파리에서 웹으로 보고 있는 상태로는 안되고 사용자가 직접 A2HS(홈 화면에 추가)를 해서 설치한 앱으로 들어가야 하기 때문이다.

그래서 manifest 파일을 추가하는 일이 가장 첫 번째 일이 되었다.

이 파일을 하나 추가하는 데에도 고민이 되었다. 그냥 static 폴더에 추가하고 `index.html`에서 불러오면 끝인 거지만. plugin을 설치하고 config에 정의해서 자동생성되게 하는게 더 좋은 게 아닐까 하는 생각이 들었다.

그 방법이 자료도 많이 나왔고, 으레 관리하기 편해보이는 방법으로 보였기 때문이다.

## Vue 플러그인 패키지 사용

- [@vue/cli-plugin-pwa](https://cli.vuejs.org/core-plugins/pwa.html)
- [register-service-worker](https://github.com/yyx990803/register-service-worker)

처음에는 위의 두 패키지를 사용했다. 튜토리얼도 잘 되어 있고, 코드가 더 깔끔하게 끝났다.

@vue/cli-plugin-pwa는 설정 파일에 적으면 알아서 `manifest.json` 파일을 생성해 주고, register-service-worker는 서비스 워커 파일을 src 에 만들고 경로를 적으면 알아서 등록해 주었다.

문제는, 해당 라이브러리가 로컬로 열 때 혹은 개발 빌드일 때는 서비스워커 파일을 생성해 주지 않는다는 것이었다. 라이브러리 설명을 보니 프로덕션 모드에서만 사용 가능하니, 프로덕션 빌드 후 테스트 해보라는 것이다. 그래서 프로덕션 빌드로 테스트를 해야 되는 건지 그러면 어떻게 해야 하는 건지 헤매고 있었다. 처음에는 의도를 잘 이해하지 못해서 서비스 워커를 개발 모드에서는 사용하면 안되는 것인줄 착각하기도 했다.

결국 두 패키지를 다시 지우고, manifest와 service worker 파일을 직접 작성하기로 했다. 프로젝트 자체가 프로덕션 빌드를 로컬에서 돌려보기 어렵게 되어있었다. 뿐만 아니라 개발 과정 중 매번 빌드를 완료하고 해당 폴더를 서버 띄워서 확인을 해야하는 것은 문제가 될 것이다.

적어도 이번에는 정말 투머치했다. 사실 라이브러리 자체의 사용법을 익히는 데에도 시간이 들었다. 해당 라이브러리가 없어도 Vue에서 PWA를 충분히 사용할 수 있다는 것을 알아내는 데에도 시간이 들었다.

아무튼 public 폴더에 `manifest.json`, `service-worker.js`(나중에 fcm 사용을 위해 파일 이름은 바꾸었다) 파일을 생성하고, `index.html`에서 직접 등록하는 코드를 추가해 주었다.

그리고 아이폰에서도 예쁘게 PWA 설치를 할 수 있게 되었다.

## 기본 코드

위의 두 패키지를 사용하지 않았다는 것. Back to basics.

### public/manifest.json

```json
{
  "name": "앱 이름",
  "short_name": "짧은 앱 이름",
  "description": "설명",
  "scope": ".",
  "start_url": "/",
  "display": "standalone",
  "theme_color": "#ffffff",
  "background_color": "#ffffff",
  "lang": "ko-KR",
  "dir": "auto",
  "orientation": "portrait",
  "icons": [
    {
      "src": "favicon.ico",
      "sizes": "64x64 32x32 24x24 16x16",
      "type": "image/x-icon"
    }
  ]
}
```

### public/service-worker.js

```js
self.addEventListener("install", (event) => {
  console.log("서비스 워커 설치", event);
});
```

### public/index.html

```html
<link rel="manifest" href="manifest.json" />
<meta name="theme-color" content="#ffffff" />
<script>
  if ("serviceWorker" in navigator) {
    navigator.serviceWorker.register("./service_worker.js").then(function () {
      console.log("서비스 워커 등록");
    });
  }
</script>
```

## 설치 방법

pc 에서는 manifest가 있으면 주소창 오른쪽 부분에 설치 아이콘이 나타난다. 클릭해서 설치하면 된다.

safari는 sonoma 버전 부터 가능한데, manifest가 없어도 알아서 앱처럽 보이게 설치해준다.(베타 버전 기준)

모바일에서 안드로이드는 설치 프롬프트 창이 뜨면 설치를 누른다. 설치 창 띄우는 것은 띄우기 전에 잠시 보관했다가 사용자가 버튼 클릭 등을 할 때 띄워 줄 수도 있다.

iOS에서는 사파리(사실 아무 브라우저나 상관없음 - 어떤 브라우저를 통해 앱을 설치하던 내부적으로 사파리임)의 공유 버튼을 누르고 ‘홈 화면에 추가’를 선택해서 설치 진행하면 된다.

### 설치 프롬프트 저장하고 꺼내 쓰는 방법

안드로이드에서만 보임.
유저가 설치 취소를 선택해도 다시 버튼을 클릭하면 띄울 수 있다.

```jsx
// 설치 프롬프트 낚아채기
window.addEventListener('beforeinstallprompt', event => {
  event.preventDefault()

  // window에 붙여버리거나 전역 store에 저장하거나..
  window.저장할이름 = event
})

// 버튼 클릭시 실행
const promptInstall = () => {
  // 저장했던 이벤트를 가져온다. window에서 혹은 store에서.
  const installEvent = window.저장할이름

  // 실행
  installEvent.prompt()

  // 유저가 '설치', '취소' 중에 어떤 것을 선택했는지 알 수 있다.
  installEvent.userChoice.then(choice => {
    if (choice.outcome == 'accepted') {
      console.log('설치')
    } else {
      console.log('취소')
    }
  }
}
```
