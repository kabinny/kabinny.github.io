---
title: "input type date + v-model"
excerpt: "input type date를 Vue 컴포넌트로 만들기"

categories:
  - Vue
tags:
  - input
  - v-model
toc: true
---

## 간단 사용법

input 요소에 공통적인 스타일을 조금 주고 싶어서 따로 컴포넌트로 만들거지만, 간단히 사용할 것이면 바로 input에 v-model을 사용하면 된다.

```html
<input type="”date”" v-model="”date”" />
```

## input을 따로 컴포넌트로 떼어두기

```js
<script setup>
  /**
   * 날짜 선택하는 곳에서 사용할 input date
   * 사용하는 곳에서는 v-model만 전달해주면 OK
   */

  const props = defineProps({
    modelValue: String,
    styles: String,
  })
  const emit = defineEmits(['update:modelValue'])
</script>
```

```html
<template>
  <input
    type="date"
    class="input-date"
    :value="modelValue"
    :style="styles"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>
```

```scss
<style lang="scss" scoped>
  .input-date {
    border: none;
    border-bottom: 1px solid $gray;
    padding: 4px 12px;
    font-size: 1.4rem;
  }
</style>
```

Vue3부터 컴포넌트 자체에 v-model을 전달할 때 받는 컴포넌트 쪽에서 사용법이 바뀌었다. 꼭 바꿔야 하는 부분이고 v-model 주고 받는 방식은 공식 문서의 컴포넌트 v-model 참고

[컴포넌트 / v-model](https://ko.vuejs.org/guide/components/v-model)

요약하자면 `modelValue`라는 프롭스 이름으로 `v-model` 값을 사용하고, 값을 업데이트 할 때는 `update:modelValue`라는 이름의 `emit`을 쏘면 된다.

컴포넌트를 사용하는 쪽에서는 딱히 바뀐 것은 없다.
`<MyDateComponent v-model="startDate" />`

## 날짜 선택을 기본 input을 사용한 이유

각자 나름의 사정이 있겠지만, Vue 3버전으로 마이그레이션 하면서 정말 유지보수 중단된 라이브러리를 많이 봤다. 그리고 외부 라이브러리까지 쓸 필요가 없는데 굳이 쓴 흔적들까지.

기존에 쓰고 있던 날짜 선택창을 띄워주는 라이브러리가 Vue 3를 지원해주지 않았고, 직접 선택을 해야하는 구조라서 사용할 때 불편하다고 생각했다. 기본으로 돌아가고 싶었다.

### 장점

- 날짜 선택하는 창 ui가 아무런 설정을 하지 않아도 알아서 잘 되어 있다. 반응형 사이즈 등을 고려하지 않아도 된다. 디바이스의 기본 ui와 어울리게 나오고, 사용자도 익숙한(어디선가 본) ui이기 때문에 사용성도 좋다(고 생각한다).
- 라이브러리 버전 호환 문제나 번들 사이즈 등의 문제가 없다..
- html 시맨틱한 기본에 충실할 수 있음 (input text에 날짜 값을 받는 것보다..)
- 브라우저가 date 타입을 지원하지 않으면 알아서 text 타입으로 fallback.
- 인풋창에서 클릭해서 입력도 가능하고, 직접 타이핑도 가능한데 어떻게 입력해도 date 값 형식인 것이 편한다. input text에 비해서 입력값을 date에 제한해서 받을 수 있다. (사용자가 마음대로 입력하지 못 함!)

### 단점

- 브라우저마다 다르게 보이고 css 커스텀에 한계가 있다. 하지만 기본 인풋박스의 색이나 테두리 정도는 커스텀 할 수 있어서 충분하다고 본다.
- 날짜 선택과 관련해서 복잡한 기능을 넣으려고 하면 힘들 수 있다.
