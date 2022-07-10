---
title: "vscode 세팅하기"
excerpt: "단축키, 확장, 설정"

categories:
  - etc
tags:
  -
last_modified_at: 2022-07-10
toc: true
---

vscode를 계속 쓰고 있지만, 언제 또 새로 세팅해야 할 지 몰라서, 지금 쓰고있는 세팅을 메모한다.

## 1. 테마

기본 테마 중에 어두운 테마 사용한다.  
테마 중에 코드 하이라이트가 마음에 안들면 커스텀도 한다.
settings.json의 `editor.tokenColorCustomizations` 이 부분에서 문법 하이라이트 컬러를 커스텀할 수 있다.

폰트는 JetBrains Mono랑 d2coding 쓰고 있었는데 컴 바꾸고 나서는 그냥 기본 폰트 쓰고 있다. 

## 2. 단축키

자주 쓰고 있는 단축키(Mac 기준)

| 사이드바 토글 | Cmd + b |
| 들여쓰기 | Tab 혹은 Cmd + ] |
| 내어쓰기 | Shift + Tab 혹은 Cmd + [ |
| 아래에 행 삽입 | Cmd + Enter |
| 현재 행 이동 | Opt + ↑↓ |
| 현재 행 복사 | Opt + Shift + ↑↓ |
| 현재 행 삭제 | Cmd + Shift + k |
| 주석 토글 | Cmd + / |

## 3. 확장

- 공통적으로 쓰이는 확장
  - Auto Rename Tag
  - ESLint
  - open in browser
  - Live Server
  - Prettier - Code formatter
  - colorize
- sass 컴파일 해볼 때
  - Live Sass Compiler
- Vue.js 사용 중일 때
  - Vetur
- 코딩테스트 연습 중일 때
  - Code Runner

## 4. prettier 설정 파일

**.prettierrc**

간단하게 평소 쓰는대로 사용하는 설정이다. 나중에 추가적인 설정은 필요할 때 하면 된다.

```
{
  "trailingComma": "all",
  "tabWidth": 2,
  "semi": false,
  "singleQuote": true
}
```

## 5. vscode web

[https://vscode.dev/](https://vscode.dev/)

웹에서도 vscode를 사용할 수 있는 것을 최근에 알았다. Microsoft 혹은 github 계정으로 로그인하면 설정을 동기화 할 수 있다. 사용환경이 진짜 거의 똑같아서 놀랍다.  
웹에서 이런 코드 에디터를 돌릴 수 있다는 것을 즉... 아이패드에서도 코딩할 수 있다는 뜻  
로컬에서처럼 서버를 띄우지는 못하지만 속도도 괜찮고 급하게 밖에서 간단히 코드 확인하고 수정할 정도는 가능할 듯하다. 서버를 띄워서 확인하려면 조금 느려도 코드샌드박스 사용해야 할 거 같다. 