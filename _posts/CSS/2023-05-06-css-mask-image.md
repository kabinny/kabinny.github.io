---
title: "css mask 활용한 간단 아이콘 스타일링"
excerpt: "아이콘 이미지가 base64인데 색도 다르고 방향도 다르고 크기도 다르다면 mask를 써보세요."

categories:
  - CSS
tags:
  - mask
  -
# last_modified_at: 2021-05-17
toc: true
# published: false
---

용량 정리를 하다보니 눈에 띄는 폰트가 있었다. 화살표 같은 것에만 사용되는데 무겁게 아이콘 폰트를 전체 불러오고 있는 것이었다.

단순한 셰브론(chevron) 모양이면 css로 네모 만들어서 border로 표현할 수도 있지만 화살표 모양까지 css로 만들면 나중에 추가하거나 수정할 때 어려울 듯 했다. 그래서 처음에는 화살표 이미지 하나 만들어서 아이콘 폰트 대신 사용하려고 했다.

이미 사용되고 있는 부분을 살펴보니 색이 다양해서 색 별로 이미지를 따로 만들어야 하나 고민을 하다가 어차피 svg를 사용할 것이기 때문에 더 좋은 방법이 있을 거 같았다. fill을 이용해서 쉽게 갈 수 있을 거 같은 느낌.

## svg to base64

문제가 생겼다. (vue 프로젝트의) scss 파일에서 이미지 경로를 root 부터 써야 한다는 것 같았다.

[https://stackoverflow.com/questions/62718645/scss-css-background-image-url-path-problem](https://stackoverflow.com/questions/62718645/scss-css-background-image-url-path-problem)

루트부터 절대 경로를 쓰는 것은 마음에 드는 방법이 아니었고, svg 모양이 단순했기 때문에 base64로 변경해서 이미지를 코드에 때려 박기로 했다.

## 다양한 색상 지정

백그라운드 이미지를 base64로 넣는 것까지는 해결했지만 svg를 생으로 쓰는 것이 아니어서 fill을 이용해 색을 넣을 수는 없었다. base64 코드 중에서 다양한 색을 적용하는 방법을 찾다가 css mask를 활용하는 방법을 알게 되었다.

[https://stackoverflow.com/questions/22384934/how-to-change-svg-fill-color-when-used-as-base-64-background-image-data](https://stackoverflow.com/questions/22384934/how-to-change-svg-fill-color-when-used-as-base-64-background-image-data)

장점은 css로 원하는 색을 지정할 수 있다는 것이다. ~~단점은 IE를 지원하지 않는 것이다.~~

참고로 저 답변들 중 상위에 있는 것중에 svg코드에서 색상 부분을 변수로 두고, 매번 인코딩해서 사용하는 방법도 흥미로웠다.

```jsx
var green = "3CB54A";
var red = "ED1F24";
var svg =
  '<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"  width="320px" height="100px" viewBox="0 0 320 100" enable-background="new 0 0 320 100" xml:space="preserve"> <polygon class="mystar" fill="#' +
  green +
  '" points="134.973,14.204 143.295,31.066 161.903,33.77 148.438,46.896 151.617,65.43 134.973,56.679 118.329,65.43 121.507,46.896 108.042,33.77 126.65,31.066 "/><circle class="mycircle" fill="#' +
  red +
  '" cx="202.028" cy="58.342" r="12.26"/></svg>';
var encoded = window.btoa(svg);
document.body.style.background =
  "url(data:image/svg+xml;base64," + encoded + ")";
```

충분히 좋은 방법이지만 js 없이 css만으로 해결하고 싶었기 때문에 css mask를 사용한 방법을 시도했다.

## mask vs clip-path

마스킹과 클리핑이 비슷해 보였지만 알고 보니 다르다!

[https://css-tricks.com/masking-vs-clipping-use/](https://css-tricks.com/masking-vs-clipping-use/)

클리핑은 벡터 패스 기반으로 만들어져, 그 패스의 안에 있으면 보이고 바깥에 있으면 보이지 않게 해준다. 일러스트레이터 등에서 벡터 이미지 제작해 봤다면 금방 감이 올 듯. 안과 밖이 명확하게 패스로 나눠지기 때문에 투명도 표현은 안된다.

마스킹은 이미지 기반으로 만들어져, 이미지의 검은 부분은 안보이고 하얀 부분은 보이게 된다. 그레이 색 농도로 투명도 표현도 가능하다. 포토샵의 마스킹 떠올리면 같은 거 같다.

## 사용 예시

라이브러리를 만들려던 것도 아니고 사용 빈도가 낮기 때문에 `main.scss`에는 기본 색만 넣기로 하고, 독특한 색이나 hover시 색 처리 등은 사용하는 쪽에서 넣기로 했다.

사용할 아이콘 svg 파일을 base64로 변환해서 `mask-image` url에 넣는다.

기본 사이즈는 width, height로 지정하고, `mask-size`를 contain으로 해서 width height만 변경하면 사이즈 조절을 할 수 있게 한다.

색상 변경은 backgroun-color를 바꾸는 것으로 할 수 있다.

추가로 화살표는 기본적으로 여러 방향으로 쓰여서 transform: rotate()로 기본 4방향은 정해 두었다.

**main.scss**

```scss
.icon-arrow {
  display: inline-block;
  vertical-align: middle;
  width: 16px;
  height: 16px;
  background-color: #000;
  mask-repeat: no-repeat;
  mask-size: contain;
  mask-image: url("data:image/svg+xml, {base64로 변환된 코드});

  &.white {
    background-color: #fff;
  }
  &.black {
    background-color: #000;
  }

  &.up {
    transform: rotate(270deg);
  }
  &.right {
    transform: rotate(0);
  }
  &.down {
    transform: rotate(90deg);
  }
  &.left {
    transform: rotate(180deg);
  }
}
```

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="css,result" data-slug-hash="abRqQdG" data-user="kabinny" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/kabinny/pen/abRqQdG">
  css-mark</a> by kabinny (<a href="https://codepen.io/kabinny">@kabinny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

## 이슈 해결

### 마스킹이 안되고 네모만 나온다면

코드펜에서 아무리해도 적용이 안되고 까만 네모만 나왔다. 아까 했을 때는 분명 됐는데 코드펜에서 해보려니 안돼서 코드펜을 탓하고 있었다.

코드펜 css 설정을 다시보니 prefix 붙이는게 없어서 webkit-mask-image 이렇게 vendor prefix가 필요했던 것이었다. 처음 했을 때 잘 됐던 것은 scss 컴파일되면서 자동으로 prefix 붙어서 그랬던 거였고..
