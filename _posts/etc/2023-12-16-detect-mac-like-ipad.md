---
title: "맥이고 싶어하는 아이패드 판별법"
excerpt: "아이패드가 MacIntel인 척 할 때.."

categories:
  - etc
tags:
  - safari
toc: true
sidebar_main: true
---

## 이상한 아이패드를 만났다

PWA를 만들면서 모바일 기기에서만 설치할 수 있게 하기로 했다. 브라우저와 디바이스를 구별해서, 설치 가능한 경우에만 manifest 파일을 추가했다. 그러면 PWA로서 설치가 가능하게 되기 때문이다.

그렇게 거의 마무리 되고 있을 때 쯤, 이상한 아이패드를 만났다. 사파리 디버깅 창을 보니까 manifest가 없었다. 아이패드니까 있어야 됐고 이전까지 테스트했던 다른 아이패드에서는 있었다.

manifest가 없어서 PWA로서 홈화면에 추가될 수가 없는데 추가된 앱을 열면 사파리가 아닌 별도의 앱처럼 열렸다. 일반적인 홈 화면 추가 기능이었다면 북마크 정도의 역할이라서 사파리로 열릴거라고 생각했는데.. 정말 이상했다. 다만 `navigator.standalone`은 true 이지만 브라우저의 상단 부분은 여전히 보이는 상태. 주소창은 없고.. 도대체 뭘까.

## navigator 비교

| navigator.     | 그동안 테스트 했던 아이패드                                                                                                        | 새로 만난 아이패드                                                                                                    | 옛날 맥북 프로                                                                                                        |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| userAgent      | Mozilla/5.0 (iPad; CPU OS 17_0_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.0.1 Mobile/15E148 Safari/604.1 | Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/16.6 Safari/605.1.15 | Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.1 Safari/605.1.15 |
| platform       | iPad                                                                                                                               | MacIntel (괘씸죄)                                                                                                     | MacIntel                                                                                                              |
| maxTouchPoints | 5                                                                                                                                  | 5                                                                                                                     | 0                                                                                                                     |

원래 그랬던건 아니고 언젠가부터 이게 시작되었다고 한다. 아이패드가 맥이고 싶은 마음은 알겠으나…

새로 만난 아이패드와 맥북을 구별하기 위해서는 `maxTouchPoints`를 체크해야 했다.

## 모바일 애플 기기 판별법

1. userAgent에 Mac이 없으면 애플 기기가 아님
2. userAgent에 iPhon, iPad, iPod도 없고 maxTouchPoints가 0이면 모바일 기기가 아님 (예를 들어 맥북.. => PWA 설치가 가능한 환경이지만 지원하지 않기로 정했기 때문에 설치를 막는다. )
3. 이것도 아니고 저것도 아니면 아이폰과 아이패드 같은 모바일 애플 기기 => 설치해도 된다.

```js
checkIOS() {
    if (navigator.userAgent.match(/Mac/i)) {
      return navigator.userAgent.match(/iPhone|iPad|iPod/i) == null &&
        navigator.maxTouchPoints == 0
        ? false
        : true
    }
    return false
  },
```

디바이스는 계속 진화하고 바뀌고 있기 때문에 이 판별법이 오랫동안 유효할 거라고는 생각하지 않는다. 하지만 일단 내부 정책상 막았어야 했었다.
