---
title: "특정 부분만 window print로 PDF 저장하기 + 파일 이름 지정"
excerpt: "깔끔하게 증명서 인쇄하기 위한 여정(?)"

categories:
  - etc
tags:
  - print
# last_modified_at: 2022-07-10
toc: true
# published: false
---

각종 증명서 PDF 다운로드 기능에 [jsPDF](https://github.com/parallax/jsPDF)를 사용했던 이유는 사용자들을 혼란시키고 싶지 않아서였다. 버튼만 누르면 자동으로 다운로드 되어 있기 때문이다. jsPDF가 별로라는 것은 아니고 처음 사용했던 방법에는 오히려 적절했다.

## 이전 PDF 문서 생성 방식의 문제점

### 첫 번째 방법

최초에 PDF 문서 생성 코드는 jsPDF를 이용해 한땀한땀 만드는 방법이었다. 텍스트를 위치시키고, 테이블을 생성하고, 이미지를 넣었다. 코드는 지나치게 복잡했고 요소의 위치 지정도 매번 PDF 생성 버튼을 눌러가며 확인할 수 밖에 없었다. 어느 코드가 어느 부분을 만들고 있는 것인지 확인도 한참 걸렸고 수정은 더 복잡했다. 그리고 새로운 양식을 추가해달라는 요청은 미룰 수 밖에 없었다.

장점: PDF 문서를 깔끔하게 만들 수 있다.

단점: 수정과 신규 작업이 어렵다. 다운로드 전 문서를 미리볼 수 없다.

### 두 번째 방법

일단 개발 경험을 개선할 수 밖에 없다고 생각했다. 그래서 PDF 문서를 jsPDF 기능으로 한땀한땀 만드는 것이 아닌 HTML로 바로 보면서 만들 수 있게 바꿨다. 사용법도 쉽고 직접 보면서 만들 수 있어서 수정이 용이했다. 어떤 양식의 문서로 새로 생성하는 것이 쉬웠다.

HTML로 만들고, [html2canvas](https://html2canvas.hertzen.com/)를 이용해 통 이미지로 만든다음, jsPDF를 이용해 1장에 이미지 1개가 들어가는 PDF 형식으로 바꿔서 다운로드 했다.

장점: 수정과 신규 작업이 간편하다. 사용자는 다운로드 받기 전에 문서를 미리볼 수 있다. 모바일에서도 잘 된다.

단점: 통이미지로 작업되어 PDF 용량이 비교적 크다. 관련 라이브러리가 무겁다.

결정적으로 많은 페이지를 저장해야 할 때 너무 용량이 크고 중간에 오류가 나는 현상이 있어서 새로운 방법을 도입하게 되었다. 일단은 이미지 퀄리티를 조금 떨어뜨려서 용량을 줄이는 것으로 임시 수정했었다.

(html2canvas를 이용한 방법은 증명서PDF 생성에서는 이제 안쓰지만, 여전히 썸네일 제조기에서 잘 쓰이고 있다.)

## 새로운 방법

사실 html2canvas 를 도입할 때 같이 고려했던 방법인데, 브라우저의 기본 인쇄 기능을 이용한 것이었다.

당시에 이 방법을 선택하지 않았던 이유

- 사용자가 인쇄 버튼을 직접 눌러야 되는 줄 알았다. (이렇게 설명하기 어려운 일을 사용자에게 시키면 문의 전화 불남)
- 내가 프린트 미디어 쿼리에 익숙하지 않았다.
- 웹페이지 전체가 아닌 원하는 부분만 프린트 하려면 어떻게 해야하는지 감이 오지 않았다.

이번에는 도입할 수 있었던 이유

- `window.print()` 로 원할 때 호출할 수 있다는 것을 알았다!
- 틈틈히 웹페이지를 책처럼 프린트하는 방법이나 프린트 미디어 쿼리를 이용한 아티클 등을 관심있게 봐 두었다. 특이한 경우였겠지만, 마크다운으로 글을 쓴다음 크롬 인쇄 기능으로 출판용 PDF 파일로 만드는 것을 봤다. 웹은 무궁무진하다..
- 구독하던 메일에서 프린트를 위한 CSS 아티클을 보고 프린트할 때 필요없는 부분을 어떻게 처리해야 할 지 감이 오기 시작했다. [CSS for printing to paper](https://voussoir.net/writing/css_for_printing)

장점: 라이브러리 사용 없음! 개발도 용이함. PDF 문서 용량 걱정도 없다.

단점: 인쇄 설정을 사용자가 직접 해야 한다. 모바일 환경이거나 크로미움 기반 이외의 브라우저에서는 원하는대로 출력이 안된다.

## 간단한 구현

프린트할 부분을 잠시 떼어 두었다가 필요없는 부분을 보이지 않게 한다. 떼어둔 부분을 body에 넣고 프린트 한다.

### 주의점

불필요한 부분을 안보이게 할 때 `display: none`으로 처리해야 한다. 처음에 만들 때 body 내부롤 아예 교체 하는 방법으로 했더니 당연하게도 Vue app이 완전 깨졌다.

프린트할 때는 프린트 요소가 body의 자식으로 들어가기 때문에 CSS 적용 범위를 신경써야 한다.

### 파일 이름 지정하기

jsPDF에서는 다운로드할 파일 이름을 원하는대로 지정할 수 있었는데, 브라우저 인쇄는 그게 안되었다. 웹사이트의 title 값이 들어가고 있어서 꼼수로 프린트하기 직전 타이틀을 바꿔두고 프린트 후 다시 원래대로 돌려두는 방법을 사용했다.

### CSS (SCSS)

A4 사이즈로 출력할 것을 기본으로 했다.

```scss
.page {
  width: 210mm;
  height: 297mm;
}

@media print {
  @page {
    size: A4 portrait;
    margin: 0;
  }
  html,
  body {
    background-color: white !important;
  }
}

// 프린트 중일 때 body 태그에 추가되는 클래스
.print-layout {
  text-align: center;

  // 프린트에서 제외할 부분에 추가할 클래스
  .print-exclude {
    display: none;
  }
}
```

### JS

```js
// 인쇄 버튼
const $button = document.querySelector("#button-print");

// 페이지 인쇄
const printPage = () => {
  // 인쇄할 페이지가 있는 부분만 가져오기
  const printContents = document.querySelector("#page-container").innerHTML;

  // 문서의 title 값을 잠시 바꿀 것이어서 원래 title 값을 저장해 두기
  const originalTitle = document.title;

  // 프린트할 부분만 넣어둘 div 요소 생성
  const printEl = document.createElement("div");
  printEl.classList.add("print-wrap");
  printEl.innerHTML = printContents;

  // body에 프린트 할 div 추가
  document.body.appendChild(printEl);
  // body에 프린트 스타일링 할 때 사용할 클래스 추가
  document.body.classList.add("print-layout");

  // 다운받을 파일 이름을 문서 타이틀로 지정
  // 코드펜에서는 코드펜 타이틀이 사용되기 때문에 다운받아서 테스트 가능
  document.title = "프린트_테스트";

  // 인쇄
  window.print();

  // 인쇄 후 돌려놓기
  document.body.classList.remove("print-layout"); // 프린트용 클래스 제거
  printEl.remove(); // 프린트용으로 복사했던 요소 제거
  document.title = originalTitle; // 타이틀 원래대로
};

$button.addEventListener("click", printPage);
```

### 크롬 인쇄 미리보기에서 설정

#### 인쇄

- 대상: PDF로 저장
- 페이지: 전체
- 레이아웃: 세로 방향

#### 설정 더보기

- 용지 크기: A4
- 시트당 페이지 수: 1
- 여백: 기본
- 배율: 기본값
- 옵션: ‘머리글과 바닥글’ 해제, ‘배경 그래픽’ 체크

## 코드펜 예시

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="YzMGyZo" data-user="kabinny" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/kabinny/pen/YzMGyZo">
  window print with custom file name</a> by kabinny (<a href="https://codepen.io/kabinny">@kabinny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
