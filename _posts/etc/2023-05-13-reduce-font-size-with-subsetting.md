---
title: "묵직한 asset 사이즈 116분의 1로 썰어버린 사연"
excerpt: "vue disable prefetch 그리고 subset 폰트 만들기"

categories:
  - etc
tags:
  - Vue
# last_modified_at: 2022-07-10
toc: true
---

오랫동안 아주 거슬렸던 파일이 있다. js 파일인데 혼자서 용량만 2.5MB. 덕분에 홈페이지 로딩 속도가 장난아니게 느렸고, 다른 일을 하면서도 어떻게 보내버릴 지 벼르고 벼르고 있었다.

파일 내용은 `module.exports = ‘base64처럼 보이는 어쩌구 아주아주아주 긴 문자열’` 이렇게 되어 있어서, 파일 자체의 용량은 못 줄이겠구나 싶었다. 폰트 파일이었기 때문에 가벼운 woff2 파일이라던가를 쓰면 되지 않을까 했지만, 해당 폰트를 쓰는 라이브러리 때문에 일단 base64로 변환된 폰트 사용을 전제로 했다.

## 필요한 페이지에서만 로드 하자

해당 파일은 특정 함수 한 곳에서만 사용되고 있었는데, 그 함수도 딱 한 곳에서만 사용되고 있었다. 그리고 메인 페이지에서도 아니고 다른 페이지에서 사용되는 것이어서 메인 페이지 로딩 시점에서 불러올 필요가 없었다.

일단 해당 함수가 전역에 붙어있는 util 파일에 있어서, 이 라이브러리를 쓰는 함수만 따로 파일을 분리했다. 그리고 사용되는 페이지 컴포넌트에서 import 해서 쓰이게 수정했다.

모든 라우트 컴포넌트를 lazy load로 해두었기 때문에 해당 페이지에 들어가야 리소스가 로딩될 것이라는 기대와는 다르게 해당 js가 로딩되고 있었다.

개발자 도구를 계속 살펴보니 prefetch 때문인 것 같았다. vue에서는 lazy load되는 컴포넌트의 리소스를 처음에 prefetch 해두는 것이 기본 true이다. 이 플러그인을 비활성화 하기 위해 vue.config.js 파일에 설정을 추가했다.

**vue.config.js** (vue2.6 (webpack))

```js
chainWebpack: config => {
  config.plugins.delete('prefetch')
},
```

prefetch를 꺼두니 메인 페이지 로딩할 때 저 무거운 js를 불러오지 않아서 이렇게 해결 되었다고 믿고 싶었다. 하지만 파일 로드를 미뤄뒀던 것일 뿐이어서 마이페이지 진입할 때 로딩이 너무 오래걸렸다.

홈페이지 첫인상이 중요하지, 로그인해야 진입 가능한 페이지에 들어갈 유저는 로딩 속도 때문에 이탈하지 않을 거야.. 라고 셀프 세뇌를 하루 정도 하고 있었는데 이대로는 찜찜했다.

## 더 좋은 방법이 있을 거 같은 희망

최근에 프리텐다드 폰트를 사용하면서 다이나믹 서브셋 이라는 것을 처음 알게 되었다. 폰트 파일이 여러개로 쪼개어져 있고, unicode-range 를 이용해 해당 웹페이지에서 사용된 글자가 있는 파일만 불러오기 때문에 아주 가볍다는 것이다.

기존에 사용하던 부분을 보니 고정된 문구만 출력하면 됐기 때문에 딱 숫자와 한글 22개 글자만 쓰이고 있었다. 만약 어떤 글자가 올 지 몰랐다면 어쩔 수 없지만, 고작 글자 몇 개 때문에 폰트 전체를 불러오고 있다니 좋은 방법이 있을 것 같았다.

## 서브셋 폰트를 직접 만들기

기본적인 서브셋 폰트 개념은 [여기](https://www.44bits.io/ko/post/optimization_webfont_with_pyftsubnet)를 참고했다. 서브셋 폰트는 폰트 파일의 경량화를 위해 사용하는 글자수를 줄이는 방법을 사용한 것이다.

이것 때문에 파이썬을 설치부터 시작하기는 귀찮아서 온라인에서 서브셋 만들어 주는 것을 사용했다.

[https://transfonter.org/](https://transfonter.org/)

폰트 파일을 업로드 하고, Characters에 사용할 글자를 넣은 뒤 Convert 버튼을 눌러 변환하면 된다.

최종적으로 사용하기 위해 base64 인코딩도 온라인으로 해결했다.

[https://www.giftofspeed.com/base64-encoder/](https://www.giftofspeed.com/base64-encoder/)

## base64로 되어 있는 폰트 파일 디코드 하기

기존 폰트 이름으로 폰트 파일을 찾아서 적용해 보았는데, 미묘하게 결과물이 달랐다. 하지만 원본 폰트 파일이 남아있지 않아서, 기존에 쓰고 있던 base64 폰트 파일을 decode 해서 활용하는 방법을 찾아보기로 했다.

일단 코드를 복붙하고 디코드 했다.

[https://www.opinionatedgeek.com/codecs/base64decoder](https://www.opinionatedgeek.com/codecs/base64decoder)

여기서 디코드된 것을 .bin 파일로 받을 수 있다.

bin에서 ttf로 변환도 온라인에서 쉽게 할 수 있었다.

[https://convertio.co/kr/bin-ttf/](https://convertio.co/kr/bin-ttf/)

이렇게 만들어진 파일로 서브셋을 만들어서 적용했다.

## 요약

base64 → bin → ttf → subset ttf → base64

대략 이런 과정을 거쳐서 원래 2.5MB였던 폰트 파일을 22KB로 용량을 줄일 수 있었다.

<img src="https://drive.google.com/uc?export=view&id=1-iJlSUB5_xIbe-oqYTuQZIY-EyQ63N-x" alt="사이즈 비교" />

해당 폰트를 사용하는 글자가 매우 한정적이어서 가능했다. 원래의 목적은 메인 페이지의 로딩 속도 개선이었기 때문에 prefetch 비활성화에서 멈췄다면 용량 줄이기 까지는 생각도 못했을 수도 있었다.

## 번외) 맥 기본 폰트 파일 위치

폰트 위치 검색해도 그냥 라이브러리/폰트에 있다고 해서 아무리 봐도 새로 추가한 폰트만 있었고 기본 폰트는 없었다.

서체 관리자에서 이리저리 보다가 폰트 이름에 커서를 올려두면 이런 저런 정보가 툴팁처럼 뜨는데 거기 생소하지만 경로가 있는 것이다. 그래서 해당 경로에 가봤더니 폰트 파일이 있었다. 왜 저기 있는 지는 잘 모르겠지만 무사히 찾을 수 있어서 다행이다. 맥북 옛날거라 그런가ㅠ

<img src="https://drive.google.com/uc?export=view&id=1tFuY499hU9ec1Wo5n5rAq1r0rDcVTlZj" alt="폰트위치" />
