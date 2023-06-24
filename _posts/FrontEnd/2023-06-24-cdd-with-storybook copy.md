---
title: "스토리북과 함께하는 협업 시스템"
excerpt: "원티드 프리온보딩 챌린지 6월 3강"

categories:
  - FrontEnd
tags:
  -

toc: true
sidebar_main: true
---

3강 제목이랑 강의 메모 내용이랑 다른거 같기도 한데, 뭐를 했었는지 메모에만 의존해야 돼서 최대한 정리해본다.
스토리북으로 문서화해서 (협업 시스템..) 사용하는 것은 4강 시간에 본격적으로 실습하게 되었다.

## 스토리북 환경 설정

저번 시간에 잠깐 했었지만, 다시 정리해 주셨다.  
(바닐라 익스트랙은 결국 강의에서 다루지 않았지만 나중에 써봐야 겠다.)

```
// 1. React 환경 (Vite)
npm create vite 디렉터리명 -- --template react

// 2. Storybook
cd 디렉터리명
npx storybook@latest init

// 3. 기타
npm install @vanilla-extract/css

// React 환경 실행
npm run dev

// Storybook 환경 실행
npm run storybook
```

## 하향식 컴포넌트 개발 Top => Down

- 이미 만들어진 페이지 혹은 큰 단위부터 만들어 놓은 경우
- 하나하나 분리하여 계층을 구성
- 컴포넌트 만들 때는 `npm run dev`를 해서, 사용자가 보는 동일한 환경으로 개발
  - 개발자가 아닌 출시되었을 때의 사용자를 위해 개발을 하며 컴포넌트를 분리

## 상향식 컴포넌트 개발 Bottom => Up

- 하나의 세트를 구성한다. 1세트 = 사용될 컴포넌트 + CSS 스타일링 + 사용될 스토리
- 사용될 컴포넌트를 내보내고, 스토리에서 사용될 컴포넌트를 불러온다.
  - 컴포넌트 (Component.jsx) - 스토리 (Component.stories.js)
- 사용될 컴포넌트 내부에는 Storybook이 무엇인지 모르게 만들어 주는 게 좋다. (스토리북과 연관된 클래스 이름이나 그런 것들을 넣지 않는 것이 좋다.)
- 다른 개발자에게 내 컴포넌트가 어떻게 사용될 지 상상하며 개발하게 된다.

## 참고하기 좋은 예시

- 스토리북 디자인 시스템
  - [https://github.com/storybookjs/design-system](https://github.com/storybookjs/design-system)
  - [https://master--5ccbc373887ca40020446347.chromatic.com/](https://master--5ccbc373887ca40020446347.chromatic.com/)
- react-use
  - 훅스계의 로대시 같은.. 컴포넌트가 있는 라이브러리도 아닌데 독스를 스토리북으로 만들었다.
  - [https://github.com/streamich/react-use](https://github.com/streamich/react-use)
  - [https://streamich.github.io/react-use/?path=/story/components-usekey--demo](https://streamich.github.io/react-use/?path=/story/components-usekey--demo)
- React Styleguidist
  - 스토리북처럼 문서화 할 수 있게 해주는 것.
  - [https://react-styleguidist.js.org/](https://react-styleguidist.js.org/)
