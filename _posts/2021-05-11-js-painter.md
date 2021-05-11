---
title: "바닐라 JS로 그림판 만들기 + 컬러 인풋"
excerpt: "canvas와 javascript로 만드는 간단한 그림판"

categories:
 - Javascript
tags:
 - vanillaJS
last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

[바닐라 JS로 그림판 만들기 강의](https://nomadcoders.co/javascript-for-beginners-2) 

javascript로 그림판을 만든다는 게 신기해서 강의도 무료고 수강해 보았다. 
어려울 줄 알았는데 강의 초급이에서 놀랐고, `canvas`가 많은 기능을 제공해 주는 것에 또 놀랐다. 

클론 코딩을 해보면서 딱히 막히는 부분 없이 무사히 완강했다. 그리고 개인적으로 넣고 싶었던 컬러 선택 기능까지 넣어 보았다. 기본 기능이 이미 잘 만들어진 상태라서 추가할 것이 적었다. 

[내가 만든 그림판 링크](https://kabinny.github.io/js_painter/)

## 컬러 선택 기능 추가하기
### 1. `<input type="color">`
index.html에  컬러 팔레트 옆에 `<input type="color">`를 추가한다. 
```html
<div class="controls__colors" id="jsColors">
      <input type="color" id="inputColor">
      ...
</div>
 ```


### 2. `input` css
추가한 `input`에 스타일을 주었다. 기본 스타일을 모두 지우고 새로 하려고 했지만 input color의 기본 구조?가 복잡해 보여서 가로 세로 사이즈 정도만 설정했다. 
```css
.controls #inputColor {
  width: 50px;
  height: 50px;
  cursor: pointer;
}
```

### 3. `input`을 불러 오고 함수 걸기
app.js에서 `input`을 불러 오고, 클릭했을 때, `handleInputColor`함수를 발동시킨다. 
```javascript
const inputColor = document.getElementById("inputColor");

if (inputColor) {
  inputColor.addEventListener("input", handleInputColor);
}
```

### 4. `handleInputColor`작성
`input`의 입력된 색상값, 즉 `value`를 가져와서 stroke와 fill 색상에 넣어 준다.
```javascript
function handleInputColor(event) {
  const color = event.target.value;
  ctx.strokeStyle = color;
  ctx.fillStyle = color;
}
```

### 5. `#inputColor` 색 변경
기존 팔레트를 클릭했을 때, `#inputColor`의 색도 같이 바뀌면 더 사용감이 좋을 것 같아서 그 부분도 수정했다. 솔직히 자세한 것은 모르지만 `input`에 입력할 수 있는 값은 hex이고 `event.target.style.backgroundColor`로 가져온 값은 rgb인 것 같다. 그래서 rgb값을 hex로 바꿀  함수를 찾아와서 추가했다.   
rgb값을 넘기면, 잘라서 각각 16진수 값으로 변환하고 hex code형식을 리턴한다. 
```javascript
function rgbToHex(colorval) {
  let parts = colorval.match(/^rgb\((\d+),\s*(\d+),\s*(\d+)\)$/);
  delete(parts[0]);
  for (let i = 1; i <= 3; ++i) {
    parts[i] = parseInt(parts[i]).toString(16);
    if (parts[i].length == 1) parts[i] = '0' + parts[i];
  }
  return '#' + parts.join('');
}
```

### 6. `handleColorClick`함수 수정
이미 가져왔던 클릭한 컬러의 배경색 값을 위에 적은 `rgbToHex`함수로 hex값으로 변환한다. 그 값을 stroke, fill, inputColor에 넣는다. 
```javascript
function handleColorClick(event) {
  let color = event.target.style.backgroundColor;
  color = rgbToHex(color);
  ctx.strokeStyle = color;
  ctx.fillStyle = color;
  inputColor.value = color;
}
```

## 새로 알게 된 점
1. `canvas`는 내부의 픽셀을 다룰 수 있고, 우클릭에서 그대로 이미지로 내보내는 기능을 사용할 수 있다. 
