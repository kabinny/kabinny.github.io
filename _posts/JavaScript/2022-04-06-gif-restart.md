---
title: "움직이는 gif 다시 재생하기"
excerpt: "이미지 캐시와 깜빡임 현상 방지하기"

categories:
 - JavaScript
tags:
 - vanillaJS
 - cache
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

정말 자주 수정해 달라고 피드백이 오는 문제가 있었다. 바로 '움직이는 gif가 다시 클릭하면 재생이 안되고 처음 재생할 때 깜빡임 있어요!'이다.

(움직이는 gif는 이후 그냥 gif로 표기하겠다.)

구현해야 하는 장면은 초기 이미지 png가 있고, 버튼 등을 클릭하면 gif로 바뀌면서 재생되는 것이다. 

## 초기 구현
**HTML**
```html
<img class="image" src="image.png" alt="image" data-url="image.gif" />
<button class="button">이미지 재생</button>
```
**JavaScript**
```javascript
const $image = document.querySelector('.image')
const $button = document.querySelector('.button')
const gifUrl = $image.dataset.url

const playImage = () => {
  $image.src = gifUrl
}

$button.addEventListener('click', playImage)
```
버튼을 누르면 이미지 소스가 바뀌면서 gif 재생이 된다. 버튼을 누를 때마다 재생을 하고 싶지만 gif가 다시 재생은 안되는 문제가 있다. 

## 다시 재생하기 기능 (캐시 방지)
캐시가 문제되므로 클릭할 때마다 url을 새로 받아오도록 수정했다. `Date.now()`대신에 랜덤 숫자를 생성해서 붙이기도 해봤다. 

**JavaScript**
```javascript
const $image = document.querySelector('.image')
const $button = document.querySelector('.button')
const gifUrl = $image.dataset.url

const playImage = () => {
  $image.src = `${gifUrl}?${Date.now()}`
}

$button.addEventListener('click', playImage)
```
버튼 클릭마다 착실히 gif가 새로 재생이 되지만 아직 문제가 남아있다. gif용량이 크거나 컴퓨터 사양 등의 문제 때문에 gif가 로딩될 때 깜빡이는 현상이 있을 수 있다. 

## 이미지 깜빡임 방지
이미지 깜빡임은 어떻게 해결해야 할 지 고민을 좀 했었다. 일반적인 상황은 아니어서 그런지 검색해도 썩 만족스러운 답이 없었다. 머리를 쥐어짜내 사용했던 방법을 소개한다. 

png 이미지외 gif 이미지를 둘 다 `HTML`에 넣는다. `z-index`로 png 파일이 앞에 보이게 하고, gif 파일은 `opacity: 0;`으로 png 뒤에 있다. 버튼을 누르면 png파일이 서서히 fadeOut된다. gif가 로딩되는 동안 png가 앞에 있기 때문에 깜빡여 보이는 현상을 해결할 수 있다. 

**HTML**
```html
<div class="ani-wrap">
  <img class="image-png" src="image.png" alt="image" />
  <img class="image-gif" src="image.gif" alt="image" data-url="image.gif" />
</div>
<button class="button">이미지 재생</button>
```

**CSS**
```css
.ani-wrap {
  position: relative;
  width: 100px;
  height: 100px;
  display: flex;
}
.ani-wrap img {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.ani-wrap .image-png {
  z-index: 2;
  opacity: 1;
}
.ani-wrap .image-gif {
  z-index: 1;
  opacity: 0;
}
.ani-wrap.on .image-png {
  animation-name: fade;
  animation-duration: .5s;
  animation-fill-mode: forwards;
}
.ani-wrap.on .image-gif {
  animation-name: fade;
  animation-duration: .1s;
  animation-direction: reverse;
  animation-fill-mode: forwards;
}
@keyframes fade {
  0% {opacity: 1;}
  100% {opacity: 0;}
}
```

**JavaScript**
```javascript
const $aniWrap = document.querySelector('.ani-wrap')
const $gifImage = document.querySelector('.image-gif')
const $button = document.querySelector('.button')
const gifUrl = $gifImage.dataset.url

const playImage = () => {
  $aniWrap.classList.remove('on')
  setTimeout(() => {
    $aniWrap.classList.add('on')
  }, 1)
  $gifImage.src = `${gifUrl}?${Date.now()}`
}

$button.addEventListener('click', playImage)
```

## 코드 예시
아마 이 gif 문제를 직면한 상황이라면 `xhtml`로 `ie`를 신경쓰면서 개발해야 될지도 모른다.(2022년에도!)
그래서 실제 사용했던 제이쿼리 버전 코드도 남겨본다. 

**jQuery**
```javascript
const gifUrl = $('.image-gif').attr('data-url')

function playImage() {
  $('.ani-wrap').removeClass('on')
  setTimeout(() => {
    $('.ani-wrap').addClass('on')
  }, 1)
  $('.image-gif').attr('src', gifUrl + '?' + Date.now())
}

$('.button').on('click', playImage)
```
