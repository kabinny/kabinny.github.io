---
title: "dayjs - time from now error"
excerpt: "공식 문서를 잘 읽자."

categories:
 - JavaScript
tags:
 - dayjs
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

dayjs는 날짜 시간 처리를 간단하게 해주는 JavaScript 라이브러리이다. 

## 문제

문제 발생 코드

```jsx
dayjs(date).fromNow()
```

에러 내용

```
Uncaught TypeError: dayjs__WEBPACK_IMPORTED_MODULE_0___default(...)(...).fromNow is not a function
```

fromNow 가 펑션이 아니라니.. 아니…!!

오탈자 있나 공식문서 예시 코드와 비교하며 눈알 빠지게 확인했다. 리액트 문제일까? 날짜 데이터가 문제인가..?  dayjs 버전 확인 (1.11.5버전이라니 뭔가 최근에 갈아엎은거 같지는 않다..)을 하고 일단 검색을 해봤다. 

너무 평범한 키워드라 그런가 검색해도 시원히 나오는게 없고, 최근 업데이트로 달라졌나 해서 최근 글로 검색해도 딱히 없고.. 아침에 이런 상황이었는데 저녁에 다시 생각해 보니 공식 문서를 다시 찬찬히 보기로 했다. 

## 해결

생각보다 금방 해결됐다.

공식문서에 [Time from now 페이지](https://day.js.org/docs/en/display/from-now#docsNav)는 설명이 짧아서 금방 볼 수 있었다. 

```
This dependent on RelativeTime plugin to work
```

플러그인이 필요하다. 

[RelativeTime 페이지](https://day.js.org/docs/en/plugin/relative-time)를 확인해서 따라 했더니 잘 작동했다. 

## 해결 코드

```jsx
import dayjs from "dayjs"
import relativeTime from "dayjs/plugin/relativeTime"

// dayjs의 time from now를 사용하려면 RelativeTime 플러그인이 필요하다.
dayjs.extend(relativeTime)

console.log(dayjs('1999-01-01').fromNow())
```

## 마무리

검색해도 나오지 않는 건 너무 쉬워서이다. 공식 문서 계속 보고 있었는데 예시 코드 바로 윗 줄을 그냥 스킵해서 본 까닭에 잠깐 당황하게 됐다. 

공식 문서를 잘 읽자.