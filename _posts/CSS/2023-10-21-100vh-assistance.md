---
title: "100vh 활용한 레이아웃을 보완하는 방법"
excerpt: "CSS 두 번 짜서 fullpage(100vh 디자인)와 타협하는 법"

categories:
  - CSS
tags:
  -
  -
# last_modified_at: 2021-05-17
toc: true
# published: false
---

## 고민의 시작

fullpage(혹은 height: 100vh로 하는 디자인) 레이아웃은 호불호가 있다고 생각한다. 개인적으로 fullpage.js 별로 좋아하지는 않는다. 이벤트성 랜딩페이지나 발표자료 등이면 상관없지만 자주 들어가서 봐야하는 서비스 페이지가 풀페이지로 되어 있으면 사용성이 안좋았기 때문이다.

1. 스크롤을 조금 했는데, 위 아래로 쓰으윽 넘어간다. 내가 원하는 만큼 스크롤을 할 수 없고, 컨트롤을 할 수 없어서 답답하다.
2. 자동으로 스크롤 되는 동안 딜레이 시간 또한 무력하게 느껴진다. 내가 뭔가 하지 않았는데 워어어어어 이거 왜 이래…이런 당황스러운 느낌이다.
3. (많은 경우) 특정 사이즈나 비율의 브라우저 창 크기에서만 멀쩡해 보인다. 사용자가 어떤 사이즈로 볼 지 모르는데.. 특정 사이즈가 아니면 레이아웃이 다 깨져보이거나 넘쳐서 안보인다거나 할 수 있다.

물론, 데스크탑 모니터 정도 큰 사이즈에서 풀 사이즈 창에서 보면 예쁠 수 있다. 깔끔하고 임팩트 있는 화면을 보여줄 수 있다.

## 해결 방법

1. 100vh로 섹션 영역을 잡아서 화면 꽉차는 레이아웃을 먼저 표현한다.
2. 브라우저 창 높이가 내용을 다 보여주지 못할 만큼 좁은 상황에서는 섹션의 높이를 100vh가 아닌 auto로 설정한다. (그리고 어색하지 않도록 적정한 여백을 추가)
3. 다양한 창 크기, 비율에서 잘 보이는지 확인한다.

## 예시 코드

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="css,result" data-slug-hash="VwqzbMg" data-user="kabinny" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/kabinny/pen/VwqzbMg">
  100vh with max-height</a> by kabinny (<a href="https://codepen.io/kabinny">@kabinny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

사실 그냥 `100vh` 대신 `100dvh` 라는 최신(?) 단위를 써보려고했다. 알아서 높이를 바꿔주는게 편리해 보였기 때문이다. 하지만 막상 적용해보니 주소창 나타났다 사라졌다 할 때 덜겅거리는 느낌이 있다. 그게 별로라고 하셔서 lvh로 바꾸어서 고정 하게 되었다.

## 마무리

무의식적으로 양자택일이라고 생각해서 열심히 이렇게 하자고 설득을 했었는데, 잘 생각해보니 그냥 내가 CSS를 두 번 쓰면 예쁘게 해결되는 문제였다. 조금 번거로워도 해결 방법은 있는 것이다.  
특정한 환경에서만 잘 되는 것으로 넘어가는 것이 아닌, 최대한 구멍을 메워가는 것이 참 뿌듯하다.
