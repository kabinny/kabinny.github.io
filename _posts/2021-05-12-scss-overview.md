---
title: "1. SCSS 개요"
excerpt: "CSS Preprocessor, SCSS 개요"

categories:
 - SCSS
tags:
 - css
 - scss
last_modified_at: 2021-05-17
toc: true
---

css는 ~~상대적으로~~ 배우기 쉽다. 웹브라우저에서 바로 확인하면서 적용해 볼 수 있어서 재밌기도 하다. 

하지만 규모가 커질수록 css의 간단한 문법이 불편해지기 시작한다.  

## CSS Preprocessor
CSS Preprocessor는 css작성을 쉽게 할 수 있는 css전처리기이다. 웹에서는 표준 css만 돌아가기 때문에 CSS Preprocessor로 작성을 하고 css로 컴파일 해야 한다. 

### Less, Sass(SCSS), Stylus
여러 전처리기, Less, Sass(SCSS), Stylus 등등, 중에 Sass(SCSS)를 사용할 이유. 

  1. 성숙도가 높다. 나온지 오래되었기 때문에 커뮤니티나 자료가 많다. -> 문제가 생겼을 때 해결 방법을 찾기 쉽다. 

  2. LESS는 기능적 한계가 있다. (LESS문법을 찾아봤는데 기본적인 사용 방법은 scss와 비슷한 거 같다.)

  3. Stylus는 기능은 좋지만 비교적 최근에 나와서 컴파일 버그 등 아직 성숙도가 떨어진다. 

### Sass와 SCSS의 차이점
SCSS는 css와 비슷하지만(sass의 훌륭한 기능을 쓸 수 있는 css같음), Sass는 {}(중괄호)와 ;(세미콜론)을 사용하지 않는다.
그리고 Mixin(재사용 가능한 기능을 만드는 방식) 사용 방법이 다르다. 

Sass는 간결하고 깔끔하고 작성할 수 있다.  
SCSS는 한 줄 작성이 가능하고 css코드 통합이 쉽다. 

## SCSS 컴파일 방법
### SassMeister (온라인)
<a href="https://www.sassmeister.com/">SassMeister</a>

온라인에서 바로 해볼 수 있는 사이트. 간단하게 해보기는 좋다.

### for 개발자
| 이름 | 링크 |
| --- | --- |
| node-sass | <a href="https://github.com/sass/node-sass">https://github.com/sass/node-sass</a> |
| gulp-sass | <a href="https://github.com/dlmanning/gulp-sass">https://github.com/dlmanning/gulp-sass</a> |
| sass-loader | <a href="https://www.npmjs.com/package/sass-loader">https://www.npmjs.com/package/sass-loader</a> |



등등 다양한 방법이 있다고 한다. 

### for 개알못
gui 환경에서 사용 가능한 컴파일러도 있지만, 써보니 뭔가 더 불편했다.

지금은 사용하고 있는 에디터인 vscode의 익스텐션 중에 <a href="https://marketplace.visualstudio.com/items?itemName=ritwickdey.live-sass">Live Sass Compiler</a>를 사용중이다. 
