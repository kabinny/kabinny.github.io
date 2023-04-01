---
title: "반응형 CSS 개선 기록"
excerpt: "vw의 저주"

categories:
 - CSS
tags:
 - SCSS
 - Vue
# last_modified_at: 2021-05-17
toc: true
# published: false
---

**프로젝트 환경**  
Vue 프로젝트, 각 컴포넌트의 스타일은 `<style lang=”scss” scoped>` 를 기본으로 함. 

반응형 스타일을 주기 위해 CSS를 작성하는 것은, 조금 귀찮을 뿐 쉽다고 생각했다. 개선이 많이 필요한 코드를 보기 전까지는. 

## 컨텐츠 폭

처음에는 잘 눈치 못 챘는데 브라우저 폭 줄이다가 너무 이상하다고 느껴서 까보니 width가 vw 단위로만 되어있었다. 

문제인 이유는 양 옆 여백이 들쑥날쑥 하고, 어떤 사이즈의 브라우저로 볼 지 모르므로 어떤 사이즈로 컨텐츠 폭이 나올 지 모르기 때문이다. - 일관적이지 않은 사용자 경험(?)을 주는 것 같다. 

```scss
// 수정 전
.inner {
  width: 26vw;
  margin: 0 auto;
  @media screen and (max-width: 1400px) {
    width: 32vw;
  }
  @media screen and (max-width: 640px) {
    width: 89.3vw;
  }
}
```

사이트 전반에 걸쳐 여러 사이즈의 폭이 쓰이고 있어서 대중소.. 정도로 정리했다. 

```scss
// 수정 후
.common-inner {
  box-sizing: border-box;
  width: 100%;
  max-width: 1080px;
  margin-left: auto;
  margin-right: auto;

  @media screen and (max-width: 1100px) {
    padding-left: 1.6rem;
    padding-right: 1.6rem;
  }

  // 좁은 폭 컨테이너
  &-narrow {
    @extend .common-inner;
    max-width: 500px;
  }
  // 중간 폭 컨테이너
  &-medium {
    @extend .common-inner;
    max-width: 768px;
  }
}
```

## 폰트 사이즈

폰트 사이즈 값으로 vw 단위를 쓰는 것을 처음봐서 기절할 뻔 했다. 

이게 어떤 이점이 있는지 현재로서는 잘 모르겠고, 브라우저의 폭을 조금만 줄여도 글자 크기가 너무 작아져서 읽기 어려운 건 확실했다. 브라우저 폭에 따라 거의 10px 아래 까지도 떨어져서, 구린 모니터로 봤을 때 읽을 수 없는 수준이었다. 큰 모니터로 꽉차게 보거나 평범한 사이즈의 모바일로 보거나 두 경우가 아니면 가독성이 떨어지는 상태였다. 

```scss
// 수정 전
h2 {
  font-size: 2vw;
  letter-spacing: -0.8px;
  @media screen and (max-width: 640px) {
    font-size: 8vw;
  }
}
```

심미적으로도 굳이 필요 없었던 자간 조정도 뺐다. 정말 자간 줄여놓은 곳이 많았는데, 자간 뿐만 아니라 `transform: scaleX(0.9)`로 글자 폭까지 줄인 곳도 종종 있었다. 

디자이너가 보통 한글 폰트 폭을 줄여놓고 시작하는 것처럼 그대로 css에 구현한 느낌이었다. 웹디자인에서는 너무 투머치 아닌가 싶기도 했고, 띄어쓰기가 잘 눈에 띄지 않고 전체적으로 가독성이 그다지 좋지도 않아서 삭제했다.   

```scss
// 수정 후
h2 {
  font-size: 2.6rem;
  @media screen and (max-width: 640px) {
    font-size: 3rem;
  }
}
```

## media query와 userAgent 동시에 사용하기

큰 화면에서는 카드 형식의 컨텐츠가 4개 보이고 화면이 작아지면 슬라이드 형식으로 바뀌는 것(을 구현하고 싶었던 것 같다).

breakpoint가 1000px이라고 치면 카드는 1000픽셀보다 작아질 때는 `display: none;`으로  안보이게 된다. 슬라이드는 userAgent를 체크해서 모바일인 경우에만 `v-if`로 렌더링된다.


문제를 재현할 수 있는 쉬운 예시는 pc에서 보지만 브라우저 폭을 좁게 할 경우이다. 카드도 안보이고 슬라이드도 안보인다. 그리고 모바일 보다 폭이 훨씬 넓은 태블릿 등으로 볼 경우 카드도 보이고, 슬라이드도 보이고 레이아웃은 와장창 깨져 보인다.  

pc로는 크게만 보고 모바일 기기로는 작게만 보면 문제가 없을 수 있지만, 우리는 다양성을 받아들여야 한다. 

이 부분은 userAgent 체크 부분을 없애고 전부 미디어 쿼리를 이용해 반응형으로 수정했다. 

## 처음부터 잘못된 것

 Vue SFC 특성상 `<style scoped>` 하면 해당 컴포넌트 내에서만 적용이 된다 해도, 이건 마음에 들지 않는다. 

- 모든 요소의 서체를 important로 박아버린 것.
- 여백 없애는 코드를 reset css에 안쓰고 여기에 쓴 것.

```scss
// 특정 컴포넌트 내부
// 수정 전
* {
  font-family: '어쩌구 폰트 이름...' !important;
  margin: 0;
  padding: 0;
}
```

지금 reset css를 건들면 어떤 사이드이펙트가 올 지 모르겠어서, 이 부분은 수정을 보류했다. 



## 마무리

작은 수정으로 큰 개선 효과를 얻을 수 있었기 때문에 뿌듯했던 작업이었다.  
오늘의 1등상은 ‘미디어 쿼리와 유저 에이전트 동시에 사용하기’에 영광을 돌린다.