---
title: "PWA에서 캐싱과 오프라인 페이지"
excerpt: "workbox, offline fallback"

categories:
  - PWA
tags:
  - PWA-Push-iOS
  - cache
  - workbox
  - service-worker
  - offline
toc: true
sidebar_main: true
---

서비스 워커에서는 fetch 이벤트를 통해 모든 네트워크 요청을 가로챌 수 있다. 받은 리소스를 캐시 스토리지나 인덱스드디비에 저장할 수 있다. 혹은 요청을 보내기 전에 캐시에 있는지 확인하고 있으면 요청을 보내지 않고 캐시에 있는 것을 꺼내서 대신 줄 수도 있다. 즉 잘만 하면 오프라인에서도 작동하게 되는 것이다.

## 워크박스 Workbox

[Workbox](https://developer.chrome.com/docs/workbox/)

워크박스는 PWA를 위한 라이브러리이다. 간단하게 캐시를 다룰 수 있게 해준다.

하지만 워크박스로 캐시를 다루고 나서 알 수 없는 에러가 생겼다. 안드로이드는 괜찮은데, 아이폰에서 앱 실행시 간헐적으로 빈 화면만 보이는 것이다. 정확히는 앱 종료 혹은 사파리 캐시 삭제 후 앱에 들어가면 빈화면 일 때가 많았다. 아이폰은 앱에서 사용자가 직접 새로고침을 할 수는 없지만, 개발자도구에서 새로고침을 눌르면 빈 화면에서 벗어날 수 있었다.

막연히 캐시 관련된 문제인 것은 알겠는데, 아무리 해봐도 원인과 해결 방법을 알 수가 없었다.

## 워크박스 문제 해결 시도

비슷한 증상을 겪는 사례들에서 해결 방법을 따라해봤는데 결국 효과가 없었다.

- 캐싱 정책 중 `staleWhileRevalidate` 를 사용하지 않고 `cacheFirst`로 수정해 보았다.
- manifest 의 start_url을 `/`에서 `/index.html`로 바꿔보았다.
- `router.js`에서 `/index.html` path도 추가해서 기존에 `/`에 있던 내용을 복붙했다. redirect로 처리하면 안된다고 했다.

### 워크박스 문제의 결국 해결된 방법

워크박스를 지웠다. 워크박스로 딱히 어떤 것을 하고 있었던 것은 아니고 캐시 처리만 했었기 때문에 지우는 데 어렵지 않았다.

그리고 캐시 방법은 [서비스 워커에서 파일을 캐싱하는 방법](https://gist.github.com/oilsinwater/ab49aebc080448062405f853124c42e8)을 참고해서 아주 베이직한 방법으로 처리했다. 기본으로 돌아가자.

## 오프라인 페이지

오프라인 상태인 경우, 홈페이지를 계속 볼 수 있게 하지 않고, 오프라인 상태임을 알려주기만 하는 것을 목표로 작업했다. 그래서 네트워크 요청들을 캐싱하지는 않지만, 오프라인 페이지를 따로 만들어서 그 html은 캐싱을 했다.

오프라인 페이지는 [오프라인 대체 페이지 만들기](https://web.dev/offline-fallback-page/)를 참고했다.

### 기본 작동 방식

1. 서비스 워커 설치될 때 오프라인 페이지(public폴더에 만들어둔 offline.html)을 캐싱한다.
2. 서비스 워커 활성화 될 때 navigationPreload를 한다(가능한 경우에만)
3. fetch 이벤트 중에 페이지 탐색을 하는 경우에만
   - preload가 있으면 그걸 내보낸다.
   - preload 없으면 그냥 fetch 한다.
   - 위의 시도가 실패하면, 캐시로 저장된 오프라인 페이지를 내보낸다.

### 참고사항

- 페이지 탐색이 아닌 경우 서비스워커에서 fetch를 간섭하지 않는다.
- preload 혹은 fetch가 실패해서 오프라인 페이지를 내보내는 경우는
  - 네트워크 에러 같은 것이 발생했을 때이다
  - 4xx, 5xx 같은 유효한 http reponse가 오는 경우에는 해당되지 않는다.

### offline.html

오프라인 페이지는 최대한 독립적으로 만들고 싶어서 폰트는 기본 system-ui 보이게 했다.

```css
font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
  Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
```

그리고 아이콘도 svg 코드를 직접 넣는 방식으로 외부에서 불러오지 않게 했다. online 이 되면 새로고침하게 했는데, 안드로이드에서 잘 작동이 안하거나 너무 느린거 같아서 새로고침 버튼도 추가했다.

대략 이렇게 구성.

```html
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>[타이틀]</title>
    <style>
      body {
        background-color: #fff;
        font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
          Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
      }
      .main {
        width: 100%;
        height: 100vh;
        text-align: center;
        line-height: 1.4;
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;
      }
      h1 {
        font-size: 24px;
        margin-block-start: 12px;
        margin-block-end: 12px;
        font-weight: bold;
      }
      svg {
        width: 100px;
      }
      p {
        margin-block-start: 12px;
        margin-block-end: 12px;
      }
      button {
        display: inline-flex;
        width: 100px;
        height: 40px;
        justify-content: center;
        align-items: center;
        color: #fff;
        background-color: red;
        font-size: 16px;
        border-radius: 10px;
        border: none;
      }
    </style>
  </head>
  <body>
    <div class="main">
      <h1>[서비스 이름]</h1>
      <p>
        <svg>[로고 svg]</svg>
      </p>
      <p>
        오프라인 상태입니다. <br />
        인터넷에 연결해주세요.
      </p>
      <p>
        <button type="button" id="buttonReload">새로고침</button>
      </p>
    </div>

    <script>
      // 온라인 상태이면 루트로
      window.addEventListener("load", () => {
        if (window.navigator.onLine) {
          window.location.href = "/";
        }
      });

      // 온라인이 되면 새로고침
      window.addEventListener("online", (event) => {
        location.reload(true);
      });
      // 버튼 누르면 새로고침
      document.querySelector("#buttonReload").addEventListener("click", () => {
        location.reload(true);
      });
    </script>
  </body>
</html>
```
