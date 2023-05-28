---
title: "jQuery 걷어내기"
excerpt: "jQuery 삭제 기록"

categories:
  - JavaScript
tags:
  - jQuery
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

예전에 처음으로 vue 공부할 때였는데, 아무것도 몰랐던 때여서 순수한 의문으로 왜 제이쿼리를 지양하는지 강사님께 질문한 적이 있다. 제이쿼리는 돔에서 요소를 일단 선택해야하기 때문에 그 과정이 비효율적이라고 하셨다. 아무래도 가상 돔을 사용하는 라이브러리/프레임워크와 직접 돔 조작을 하는 제이쿼리랑 같이 사용하는 것은 바람직하지 않을 듯 하다.

> jQuery가 나쁘다 (X)  
> Vue.js 혹은 React 프로젝트에서 jQuery를 사용할 수 없다/사용해서는 안 된다 (X)  
> jQuery를 쓰면 가상 돔 활용 라이브러리의 이점이 줄어든다 (O)

아래의 예시들은 완전한 정답은 아니겠지만, 이렇게 처리하고 지나갔다 정도로만 봐야 한다.

딱히 제이쿼리를 개조한 흔적은 없었기 때문에 `$(`으로 검색해서 나오는 모든 부분을 체크해서 수정했다.

## 요소 선택

Vue나 React 같은 SPA 만드는 것에서는 기본적으로 돔 요소 선택을 ref로 하게 된다.

그 외 ref 쓰기 애매한 부분은 document.querySelector를 사용했다.

```js
// Vue
this.canvas = $('canvas')[0]
↓
this.canvas = this.$refs.canvas
```

### body 선택

body 태그를 선택할 일은 모달 창 띄울 때 배경에서 스크롤을 막기 위한 클래스를 넣고 뺄 때이다. 하지만 body 태그는 vue의 바깥에 있기 때문에 ref를 이용해 선택할 수 없다.

일단 `document.body`로 선택했고, 클래스 추가 삭제는 각 `classList.remove` 와 `classList.add` 를 사용했다.

```js
$('body').removeClass('stop-scrolling')
$('body').addClass('stop-scrolling')
↓
document.body.classList.remove('stop-scrolling')
document.body.classList.add('stop-scrolling')
```

## 요소 크기와 위치

innerHeight, offset top 등 요소의 크기나 위치를 나타내는 부분은 거의 비슷했다. 다만 요소가 렌더링 완료되기 전에 수치를 가져오려고 하면 원하는 값이 나오지 않기 때문에 주의가 필요하다.

이전 코드는 ref로 선택한 거를 제이쿼리로 다시 선택하고… innerHeight를 가져오고 있었다.

innerHeight는 clientHeight로 바꿨다.

offset().top() 은 .offsetTop 으로 바뀐다.

그리고 요소를 계속 선택하는 부분을 수정했다.

```js
let top = window.scrollY + 'px'
if (
  window.scrollY + $(this.$refs.myMenu).innerHeight() >=
  $('.container-views').offset().top +
  $('.container-views').innerHeight()
) {
  top =
    $('.container-views').innerHeight() -
    $(this.$refs.myMenu).innerHeight() +
    'px'
}

↓

let top = window.scrollY + 'px'
const containerViews = document.querySelector('.container-views')

if (
  window.scrollY + this.$refs.myMenu.clientHeight >=
  containerViews.offsetTop + containerViews.clientHeight
) {
  top =
    containerViews.clientHeight -
    this.$refs.myMenu.clientHeight +
    'px'
}

```

## 이벤트

윈도우에 스크롤 이벤트를 추가하면 추가한 코드가 있는 컴포넌트가 파괴된 후에도 이벤트는 남아있기 때문에, 파괴 전 삭제를 잊지 말자.

```js
mounted() {
  $(window).scroll(() => {
    this.scroll_position = $(window).scrollTop()
  })
}

↓

mounted() {
  window.addEventListener('scroll', this.onScroll)
},
methods: {
 onScroll() {
    this.scroll_position = document.documentElement.scrollTop
  },
},
beforeDestroy() {
  window.removeEventListener('scroll', this.onScroll)
},
```

## CSS

JavaScript로 CSS 수정을 할 때는, `요소.style.선택자 = 값`

선택자가 background-image 처럼 ‘-’로 이어진 단어라면 backgroundImage 와 같이 카멜 표기법으로 써준다.

(라이브러리가 생성하는 네비게이션 요소라서 querySelector로 선택했다. )

```jsx
$('#nav').css('opacity', '0')
↓
const $nav = document.querySelector('#nav')
$nav.style.opacity = 0
```

## 마무리

로컬 파일을 불러와서 쓰고 있어서 그 파일도 지웠는데, 생각보다 용량이 작았다. 작게 느껴졌다. 물론 파일 용량만의 문제는 아니었지만, jQuery 지운 것만으로도 후련한 기분이 든다.
