---
title: "Vue.prototype을 마이그레잇!"
excerpt: "Vue3에서 inject 값의 타입이 unknown으로 나올 때 해결법"

categories:
  - Vue
tags:
  -
toc: true
---

전역 속성을 마이그레이션 하는 데에 여러 단계를 거친 것 같다. Vue 3버전이 되면서 `Vue.prototype. ...` 이렇게 일단 붙여서 this..로 사용하는 방법이 사라져서 처음에는 막막했다. 하지만 다 수정해놓고 보니 공식문서에 너무 잘 나와 있다. 다만 초반에 이건 이렇게 수정, 마이그레이션 해야 된다는 길을 찾는 데에 확신이 없었다.

## 1단계 일단 globalProperties로 바꾼다

```js
// main.js

// Vue2
Vue.prototype.$utils = utils;

// Vue3
app.config.globalProperties.$utils = utils;
```

Vue 초기화 부분은 생략.. 다짜고짜 Vue에다가 갖다 붙였던 2버전 사용법과 달리 3버전에서는 `<template>` 안에서 바로 접근해서 사용이 가능하다. `<script>` 안에서도 이전과 같이 사용 가능하다.

이렇게 아무 문제 없이 넘어갈 수 있을 줄 알았지..

## 2단계 provide - inject 방식으로 바꾼다

아직은 둘 다 사용할 수 있게 globalPropereties를 삭제하지 않고 provide를 추가했다.

globalProperties는 옵션 API 의 this로 접근 하는 것이어서 컴포지션 API setup 과 사용하기가 어려웠다.. 컴포지션 API 다운 방식으로 값을 멀리 보내기 위해서는 provide-inject가 필요했다.

```jsx
// main.js
import utils from "utils.js";
app.provide("$utils", utils);

// sample.vue <script setup>
import { inject } from "vue";
const $utils = inject("$utils");
```

그냥 pinia로 옮겨야 되나 고민을 조금 했는데, 기존에도 store 와 별개로 전역 속성으로 있던 것이라 일단 그대로 두기로 했다. 사용법은 리액트의 useContext와 같은 흐름.

메이저 버전 마이그레이션이 이렇게 쉽게 끝날리가 없다.

## 3단계 심볼키를 사용하고 타입을 추가한다

그리고나서 프로젝트에 타입스크립트를 추가하게 되면서, 새로운 타입 문제에 맞닥뜨렸다.

정확히 말하면 inject해서 쓰고 있던 $utils의 타입이 unknown으로 나왔기 때문에 방법을 찾다가, provide-inject에 제대로 타입을 추가하려면 심볼키를 쓰는게 편하다는 것을 알게 되었다. 2단계에서 사용한 방법은 그냥 문자열 키를 사용한 것이었다.

제공할 때도, 사용할 때도 미리 만들어둔 심볼키를 사용한다고 생각하면 된다.

```jsx
// keys.ts
// 심볼키를 만들고 내보낸다.
import type { InjectionKey } from 'vue'
import type { 미리정의한타입 } from 'utils.ts'

export const $utilsKey = Symbol() as InjectionKey<미리정의한타입>

// main.ts
// 심볼키를 이용해서 provide
import utils from 'utils.ts'
import type { $utilsKey } from 'keys.ts'

app.provide($utilsKey, utils)

// sample.vue <script setup>
// 심볼키를 이용해서 inject
import { inject } from 'vue'
import type { $utilsKey } from 'keys.ts'

const utils = inject($utilsKey)
```
