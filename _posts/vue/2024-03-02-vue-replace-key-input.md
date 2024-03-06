---
title: "Vue에서 키보드 입력값 대체하기"
excerpt: "dispatchEvent 사용해보기"

categories:
  - Vue
tags:
  -
toc: true
---

## 특수한 상황

평소에는 전혀 생각지도 못한 부분이었는데, 점을 누르면 숫자 0이 3개 들어가게 할 수 없냐는 요청이 들어왔다. 정확히는 키보드의 숫자키패드 부분에 있는 점(period)을 눌렀을 때이다.

은행 전산 시스템에서는, 은행마다 다를 수 있겠지만, 쭉 그렇게 써 오다가 못 쓰게 된 기능이라 불편하다고 하셨다.

그리고 큰 금액을 수동으로 입력을 해야 하는데, 0이 많으면 실수하기 쉽고 번거롭다.
당장 츄라이 해봐야 했다.

## 예시 코드

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="poYaLBv" data-user="kabinny" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/kabinny/pen/poYaLBv">
  replace key input - vue</a> by kabinny (<a href="https://codepen.io/kabinny">@kabinny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

약간의 뻘짓과 챗지피티의 도움으로 어찌어찌 만들어서 일부 적용해 보았다.

1.`@keydown="handleKeydown"` input에 keydown 이벤트를 추가한다. 입력값은 `v-model`로 추가한다.

2.키보드 이벤트에서 키코드를 뽑아내서 '그' 키를 누른 건 지 확인한다.

숫자 키패드(numpad) 영역에 있는 .(period)의 코드는 'NumpadDecimal'이다. 인터넷에 누른 키의 코드를 알려주는 사이트에서 알아냈다.

(나는 텐키리스 키보드 써서 저 키가 없었다. 개발 중에는 다른 코드값으로 했다가 테스트는 다른 키보드로 했다..)

3.'그' 키를 눌렀다면 기본적인 입력 기능을 막기 위해 `preventDefault()`를 한다.

4.타깃 값에 '000'을 호로록 추가한다.

5.여기까지만 하면 될 줄 알았는데 값이 업데이트가 안되고 있었다... Vue 개발자 도구에서 v-model 값을 뚫어지게 봤는데 요지부동.

6.v-model을 업데이트 해주기 위해서 input 이벤트를 한 대 쳐주었다.

더 좋은 방법을 알게 되면 정말 좋겠다..

```js
// numpad에 있는 period 를 눌렀을 떄 '000' 입력
handleKeydown(e) {
  const { target, code } = e

  if (code === 'NumpadDecimal') {
    e.preventDefault() // 원래 입력 동작 방지
    target.value += '000' // '000'을 추가
    target.dispatchEvent(new Event('input')) // v-model 업데이트
  }
},
```
