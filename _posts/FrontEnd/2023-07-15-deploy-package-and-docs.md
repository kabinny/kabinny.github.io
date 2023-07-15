---
title: "내가 만든 컴포넌트 디자인 문서화하고 배포하기"
excerpt: "원티드 프리온보딩 챌린지 6월 4강"

categories:
  - FrontEnd
tags:
  - storybook

toc: true
sidebar_main: true
---

사실 강의 초반에 너무 빨라서 좀 놓쳐서 당황했다. 그래서 한동안 다시 실습해볼 엄두가 나지 않았는데 마무리를 짓고 싶어서, [완성 코드](https://www.npmjs.com/package/cdd-storybook-wanted)를 보고 하나하나 따라해 보았다.

직접 컴포넌트 라이브러리는 만들어 보는 내용이다.

## 컴포넌트 라이브러리 만들기

### 준비

- npm 계정
- Github 계정

### 만들기

1. 새로운 저장소를 만든다. `mkdir sb-test`
2. npm 초기화 `npm init -y`
3. 필요한 패키지 설치  
   리액트는 사용될 패키지에서 쓰일 버전과 충돌이 있을 수 있어서 peerDependencies로 설치했다.  
   컴포넌트를 만들 때 사용할 styled-components, props-types를 설치했다.  
   패키지 빌드를 위해 rollup, rollup-plugin-esbuild, esbuild를 설치했다.

4. 버튼 컴포넌트 작성
5. 스토리북 추가  
   스토리북은 빈 폴더에 설치하는 게 아니어서 리액트 코드를 먼저 작성하고 추가했다. `npx storybook@latest init`  
   초기 설정을 vite, webpack중에 고르는게 있었는데 vite로 잘못 골라서 나중에 webpack으로 패키지랑 설정을 수정했다..  
   그리고 기본 생성되는 예시 스토리들은 삭제했다.
6. 버튼 스토리 작성
7. 스토리북 config 수정
8. 번들러 설정

## npm 배포

해당 저장소에서 npm 로그인을 한다. `npm login`  
브라우저가 열리고 거기서 로그인 하고 다시 돌아오면 된다.  
npm 배포 `npm publish`를 하면 바로 npm에 배포 된다. 이렇게 갑자기?

npm 페이지에서 보일 README.md 파일을 수정했다. 개발 환경, 설치, 사용법, 문서 링크, 라이센스를 적었다. 문서를 자세히 적어 두는 것는 중요한 거 같다.

저장소에 추가로 수정하고, 커밋한다고 자동으로 npm에 배포가 되는 것은 아니(었)다.
[npm version 업데이트할 버전](https://docs.npmjs.com/cli/v9/commands/npm-version) 명령어로 버전을 수정하고 다시 `npm publish`로 배포해주면 된다.

## storybook 문서 배포

버전이 바뀌어서 그런가 아무튼 스토리북이 빌드되는 폴더 이름이 달라진 거 같았다. 깃허브 페이지로 배포하려면 `docs`에 빌드되어야 하는데.. 스토리북 문서를 찾아보다가 내보내기 폴더 지정하는 옵션을 찾았다.

스토리북 빌드 스크립트

```json
    "build-storybook": "storybook build -o docs",
```

빌드한 파일도 깃에 추가하고 깃허브에 가서 Pages 설정에서 docs폴더를 배포할 거라고 해둔다.

스토리북으로 문서까지 배포하니까 너무 멋진 패키지가 될 것만 같다.

## 추가로 하면 좋은 것

- 타입스크립트로 전환
- Github Actions, Workflow 작성: 커밋 메세지를 확인해서 버전을 올리고, Release 문서도 생성하고, 배포하기 까지 자동으로!

## 마무리

간단한 버튼 컴포넌트 였지만 직접 패키지 배포하고 설치해서 써보는 건 신기하고 뿌듯했다. 그냥 쭉 보는 거랑 해보는 거랑은 다르네. 정리를 이렇게 밖에 못하다니 아쉽다.. 그리고 공부해야 할 것은 정말 많군.
