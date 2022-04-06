---
title: "0) vue 시작 전 준비"
excerpt: "nvm설치하기, vue공식 문서 보기"

categories:
 - Vue
tags:
 - node.js
 - nvm
# last_modified_at: 2021-07-17
toc: true
---

vue를 본격적으로 공부하기 전에 해둘 것이 있었다. 
대부분의 내용을 <a href="https://heropy.blog/2018/02/17/node-js-install/">여기</a>를 참고한다. 

## git 설치
git 설치 안되어 있으면 설치하기.

## homebrew ⇒ NVM ⇒ node.js
homebrew로 설치하는 게 관리하기 편한 것 같다. 

### node.js 설치
npm을 사용할 것이라 node.js설치해야 한다. 예전에 설치했지만, nvm설치를 위해 삭제하고 다시 깔았다. M1용 개발 셋팅은 조금 다른 경우가 많아서 찾아보고 해야 한다. 

### nvm 설치
nvm은 node.js의 버전을 관리해 주는데, node.js보다 먼저 설치해야 한다. 나중에 설치해도 되는 n이라는 것도 있다. 같이 공부하는 분들과 맞추고 도움받기 쉽게 하기 위해 그냥 nvm을 선택했다.

nvm으로 node.js버전 선택하고 사용하기

```bash
$ nvm install 12.14.1
$ nvm use 12.14.1
```
#### 설치중 에러
homebrew로 nvm 설치 했는데 커맨드 낫 파운드..
[command not found](https://stackoverflow.com/questions/27651892/homebrew-installs-nvm-but-nvm-cant-be-found-afterwards/37613432)

## vue 공식 문서
vue는 한국어로 공식 문서가 잘 되어 있다. 3버전은 나온 지 얼마 안돼서 지금 가장 많이 쓰이고 있을 2버전으로 공부하고 3버전에 추가된 기능 등을 봐도 된다-고 한다.
 
- [Vue2 공식문서](https://kr.vuejs.org/v2/guide/index.html)
- [Vue3 공식문서](https://v3.ko.vuejs.org/guide/introduction.html)

## extensions
vscode에서 vue관련 확장은 'vetur' 검색해서 설치. 


크롬에는 뷰 개발도구 설치할 수 있다. 사람에 따라 잘 쓰는 사람 있고 안쓰기도 하는 듯 하지만 일단 설치했다. 웹사이트 접속했을 때 뷰로 만들었는지 알 수 있는 점이 좋다. 
[vuejs-devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
