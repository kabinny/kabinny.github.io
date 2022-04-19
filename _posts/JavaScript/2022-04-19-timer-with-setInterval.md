---
title: "간단한 timer 만들기"
excerpt: "setInterval, clearInterval 사용하기"

categories:
 - JavaScript
tags:
 - vanillaJS
 - setInterval
 - clearInterval
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

클릭해서 답을 맞추는 간단한 퀴즈 게임을 만들다가 타이머 부분만 정리해보려고 한다. 실제 게임에서는 오디오 재생과 정답을 맞췄을 때 타이머가 잠깐 멈추고 틀렸을 때 타이머가 5초 깎이는 등의 기능이 더 있다. 


## 만들어야 하는 기능
총 시간이 30초이고 타이머 바가 점점 줄어든다. 
시간이 다 되면 얼럿 창이 뜬다. 
타이머 시작, 일시정지, 재시작 기능을 만들어야 한다. 

setInterval 시작하기, 일시 정지 하기(clearInterval 사용하기)

## 화면 구성
`.timer`가 타이머 전체 영역이고 그 안에 있는 `.bar`가 시간이 지남에 따라 점점 줄어들 것이다. 
### HTML
```html
<div class="timer">
  <div class="bar"></div>
</div>

<button id="btnStart">startTimer</button>
<button id="btnPause">pauseTimer</button>
<button id="btnRestart">restartTimer</button>
```

### CSS
```css
.timer {
  height: 2rem;
  background-color: lightgray;
  position: relative;
}
.timer .bar {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: cyan;
  top: 0;
  right: 0;
}
```

## setInterval 기능
200 ms 마다 남은 시간을 200 ms 줄이고, 타이머 바 길이도 줄어들게 하는 함수를 반복할 것이다. 일단 반복해야 하는 함수 `timer`를 만든다. 
```javascript
const timeLimit = 30000 // 제한 시간
let timeLeft = timeLimit // 남은 시간

const timer = () => {
  if (timeLeft <= 0) {
    stopTimer() // 남은 시간이 0이면 반복 중지
    alert('시간이 끝났습니다!')
  }
  
  timeLeft -= 200
  
  $bar.style.width = timeLeft / timeLimit * 100 + '%'
}
```


timer를 반복한다. setInterval을 이용해서 반복하는데 이걸 workingTimer에 저장한다. 
```javascript
let workingTimer 

const startTimer = () => { // 시작 버튼 누르면 이것 실행
  workingTimer = setInterval(timer, 200)
}
```

## clearInterval 기능
timer를 멈추고 싶으면 workingTimer 를 clearInterval 하면 된다. 
```javascript
const stopTimer = () => {
  clearInterval(workingTimer)
}
```

다시 시작하려면 타이머를 멈추고, 남은 시간을 초기화하고, workingTimer에 다시 타이머 반복을 할당한다. 
```javascript
const restartTimer = () => {
  stopTimer() // clearInterval(workingTimer)
  timeLeft = timeLimit
  startTimer() // workingTimer = setInterval(timer, 200)
}
```

## 최종 코드

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="MWrLywQ" data-editable="true" data-user="kabinny" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/kabinny/pen/MWrLywQ">
  simple-timer</a> by kabinny (<a href="https://codepen.io/kabinny">@kabinny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>