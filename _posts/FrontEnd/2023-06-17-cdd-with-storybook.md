---
title: "스토리북과 함께하는 CDD"
excerpt: "원티드 프리온보딩 챌린지 6월 2강"

categories:
  - FrontEnd
tags:
  -

toc: true
sidebar_main: true
---

## 아토믹 디자인 패턴

아토믹 디자인 패턴에 대해 잘 이해가 되지 않았었는데 어느 정도 알 거 같다.

아토믹 디자인 패턴이 최고라거나 무조건 좋다는 것은 아니지만, 아톰을 조립하는 컨셉의 디자인을 제시한다는 의미가 있다.

[Atomic Design by Brad Frost](https://atomicdesign.bradfrost.com/)

### 인터페이스

[자동차 인터페이스 디자인 - 박수레] 책의 UX 용어 설명

> 인터페이스: 사용자가 인공물을 쉽게 동작시키기 위해 필요한 시스템을 의미한다. 흔히 버튼이나 슬라이더 같은 조작부를 일컫기도 한다.

> UI: 사용자 인터페이스(User Interface)의 줄임말로, 문맥상으로 화면상의 버튼, 스위치 등의 조작부를 일컫는다.

인터페이스는 사회적 합의, 약속 같은 것으로 이해했다.

### 아토믹 디자인에서 인터페이스

아토믹 디자인 방법론을 구성하는 기본 단위: 인터페이스

아토믹 디자인 패턴에 따라 개발을 하면 인터페이스를 구성하는 단위를 명확하게 구분할 수 있다. 컴포넌트를 재사용하기 때문에 일관적인 디자인 시스템 유지, 효율적 개발이 가능하다.

- Atom 원자: 인터페이스를 구성하는 가장 작은 단위
- Molecule 분자: Atom을 조합하여 만든 더 복잡한 컴포넌트
- Organism 유기체: Molecule을 조합하여 만든 더 복잡한 컴포넌트
- Template 템플릿: Atom, Molecule, Organism을 조합하여 만든 더 복잡한 레이아웃
- Page 페이지: Templage, Organism을 조합하여 만든 페이지

아토믹 디자인 패턴은 자료를 찾아보면 미세하게 기준이 다르거나 그렇다..
원자 단계는 조합이 없고, 그 다음 분자부터 조합이 있다. 구성이나 기준의 정답은 없다.

정답은 없지만 만들어내신 분의 UI적 예시는 있다. 컴포넌트를 개발하면서 단위별 정의는 각자 다 달라질 것이다.

<figure>
  <img src="https://atomicdesign.bradfrost.com/images/content/instagram-atomic.png" alt="instagram atomic" />
  <figcaption>https://atomicdesign.bradfrost.com/chapter-2/</figcaption>
</figure>

### 정리

아토믹 디자인 패턴은 정답은 아니다.

**하지만 개발을 배우며 학습하는 관점에서는 정말 좋은 예시이자 방법론이다.**

사람마다 기준이 다른 이유: 제시한 사람이 UI로 설명해줘서 개발을 하는 사람 입장에서는 생각하는 게 달라짐.

서비스의 디자인 시스템이 항상 일관적이지는 않았던 거 같다. 끊임없이 여기저기 업데이트를 해나가는데. 이 컴포넌트의 디자인을 수정한다고 해도, 이 컴포넌트가 쓰인 모든 상위 컴포넌트나 디자인을 바꾸지는 않는 경우. 업데이트된 컴포넌트를 새로 만들거나 해야된다.

아토믹 디자인 패턴을 따라서 개발하는 것이 지키지 못할 약속이라는 게 정말 맞다. 하지만 아토믹 디자인 패턴을 적용하면서 뭔가 작은거라도 만들어 보면 많은 공부가 될 것 같다. 기약없는 다음 사이드 프로젝트에는 시도해 봐야 겠다.

- 리액트 컴포넌트를 만들 때 아토믹 디자인 패턴을 도입하는 것이 아닐까?
- 아토믹 디자인 패턴을 구성하는 기준 + 리액트스러운 컴포넌트 조합 + 회사에서 추구하는 비즈니스 가치, 도메인 컨텍스트 고려
- 거기에 더해서 TPO 고려 (시간, 장소(프로젝트), 상황)

## 상향식 컴포넌트 개발과 하향식 컴포넌트 개발

### 상향식 컴포넌트 개발 Bottom -> UP

- 컴포넌트 -> 페이지, 아토믹 디자인 패턴
- 가장 작은 단위의 컴포넌트들을 먼저 개발
- 하위 컴포넌트를 조합해서 전체적인 컴포넌트 완성
- 숙련도가 부족하다면 YAGNI에 빠지기 쉽다.
  - You Ain't Gonna Need It: 프로그래머가 필요하다고 간주할 때까지 기능을 추가하지 않는 것이 좋다는 익스트림 프로그래밍의 원칙.

### 하향식 컴포넌트 개발 Top -> Down

- Next.js에서 Page -> (작고 하찮을 정도로 귀여운) 컴포넌트
- 전체 시스템의 큰 구성요소들을 먼저 개발
- 이후에 중복되는 컴포넌트를 분리
- 숙련도가 부족하다면 르블랑의 법칙에 빠지기 쉽다.
  - Leblanc's Law: 나중은 결코 오지 않는다. -> 나중에 하자고 하지만 하지 않는다. 한번 작성한 쓰레기 코드를 나중에 수정하는 일은 결코 없다.

## 제어 컴포넌트와 비제어 컴포넌트

### Controlled Component

> Push & Single Source of Truth

- 단일 진실 원천!
- 사용자가 입력하면 값을 계속 push 한다.
- 이벤트 핸들러를 항상 작성해야 한다. (push 해야하기 때문)

=> 내가 제어하고 내가 푸시. 진실의 원천 상태가 계속 유지됨.

```jsx
<input type="text" value="inputValue" onChange="handleChange" />
```

### Uncontrolled Component

> Pull & get State

- 이벤트 핸들러를 통해 inputRef.current.value를 변경하지만
- 항상 쓸 필요는 없다. < 이게 핵심
- 비제어 컴포넌트는 필요한 시점에 트리거 하기 때문

=> 내가 관여하지 않음. 값이 필요할 때 꺼내온다.

```jsx
<input type="text" ref="inputText" />
```

### 제어, 비제어 컴포넌트가 왜 중요한가

- CDD(컴포넌트 주도 개발)에 엄청난 영향을 끼친다
- 컴포넌트를 만들고 구성하는데 코드레벨, 설계에 업청나게 영향을 준다

## 스토리북 상호작용 테스트

테스트 부분은 제대로 해본 적이 없어서 잘 와닿지가 않는다. 좀 더 공부해 봐야 할 것 같다..

<figure>
  <img src="https://martinfowler.com/bliki/images/testPyramid/test-pyramid.png" alt="test pyramid" />
  <figcaption>https://martinfowler.com/bliki/TestPyramid.html</figcaption>
</figure>

TDD 테스트 피라미드: UI 테스트는 비싸고 느리다

요즘은 테스트 트로피다!!!

[https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications](https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications)

<figure>
  <img src="https://pbs.twimg.com/media/DVUoM94VQAAzuws?format=jpg&name=900x900" alt="test trophy" />
  <figcaption>https://twitter.com/kentcdodds/status/960723172591992832</figcaption>
</figure>

올바른 테스트 전략을 선택하는 것: 벽을 칠하기 위한 붓을 선택하는 것

Storybook + Chromatic 조합으로 테스트를 할 수 있다.

- 컴포넌트 주도의 독립적인 개발 환경 제공
- 스냅샷 테스트 지원
- 테스트에 용이한 Addon 지원
- (비쌈)

## 스토리북 설치

### vite

react 템플릿으로 일단 프로젝트 시작.

### storybook

예전 스토리북은 써본 적이 없어서 몰랐는데, 예전 버전에는 설정 해야할 것이 많았다고 한다. 최근 나온 7버전 기준으로 설치 후 따로 설정 없이 바로 사용 가능.

이미 만들어진 폴더에 init을 하면 된다.

### vanilla-extract

처음 들어 보는 거여서 어떤 건지 이해하려고 해봤다.

~~CSS 인데 TS라고?~~

CSS in JS 좋아하는 사람들이 좋아할 거 같이 생겼다. CSS in JS처럼 작성하지만, 빌드될 때 css로 빌드 되어서 제로 런타임이라는거 같다. CSS in JS는 작성하다 보면 코드가 너무 억지스러워서 많이 좋아하지는 않지만, 적어도 이거는 CSS in JS의 비용 문제는 해결된 것인듯.

[vanilla-extract — Zero-runtime Stylesheets-in-TypeScript.](https://vanilla-extract.style/)

(...2주 지나서 다시 생각해보니 Vue의 SFC 같은 방식을 떠올리면, CSS in JS를 좋아할 수도 있다고 이해할 수 있었다! )

## QnA pick

### 상향식 하향식 어떤 방식

팀의 숙련도가 좋다면 혼합하고, 숙련도가 떨어진다면 **무조건** 하향식.

### 요즘 사전 과제

몇 년 전에는 순수 자바스크립트로 뭐 구현하기 과제가 유행이었다. 지금도 근데 직접 채용하는 회사에서 내는 과제가 아니면 종종 보이는 듯 하다.

요즘은 리액트로 구현해 보기 과제가 많다(고 한다.)

순수 자바스크립트 과제가 줄어든 이유는 …

- 어차피 같은 코드 복붙이 되어버렸다고 한다.
- 순수 자바스크립트 실력(?)을 보고 뽑았더니 리액트는 잘 못하고, 과제도 남의 JS 패턴을 보고 외운 것(이더라.)

⇒ 회사에서 쓸만한 과제를 내는 방향으로…

예전에 지원자 너무 많다고 사전 과제 안내 미루다가 결국 아무말없이 연락이 안 온 회사가 생각났다.

### 요즘 신입

회사마다 다르겠지만 요즘 신입들은…

- 대부분 부트캠프 출신
- IT연합동아리 이력 (이런건 도대체 어떻게 하는 거지..)
- 기본 TypeScript 사용 (난 아직 아닌..)
- Next.js 공부중
- TanStack Query 사용중

요즘 신입 너무나도 대단하다.

### 요즘 부트캠프

요즘 부트캠프.. 2개 이상 다니는 사람이 많다. 다들 어떻게 그 시간과 재력을 끌고오는 건지.. 부트캠프 할까말까 고민했을 때 가장 문제가 됐던 것은 비싼 수강료는 둘째고, 긴 투입 시간이었다. 일하면서 하기는 거의 불가능 한데, 6개월은 해야 되니까.. 그리고 커리큘럼 보면 6개월도 너무 짧다. 그래서 부트캠프를 2개씩 하나보다.

근데 부트캠프에서 확실히 잘했던 사람, 즉 아-저 사람은 취업 할 거 같아 라고 생각했던 사람들은 이미 취업했었던 중고 신입이거나 부트캠프 했던 사람 아무튼 뭔가 하다가 온 사람들이었다.

놀라운 것은 동시에 부트캠프를 2개 다니는 사람도 많고, 5개까지 다니는 사람도 있다는 것..

부트캠프의 장점 중 하나인 ‘많은 동기들과 발휘하는 집단지성’ 이거를 어느 부분 기대하고 부트캠프를 갔지만, 너무 저조한 참여에 전혀 누리지 못한게 아쉽다. 아무래도 시험을 쳐서 들어가는 부트캠프가 아니어서 그런지 탈주자 너무 많아서 분위기도 음..

아무튼 부트캠프에 미쳐있는 것만 아니면.
