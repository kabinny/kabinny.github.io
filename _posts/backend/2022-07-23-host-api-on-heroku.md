---
title: "Heroku에 json-server 호스팅하기"
excerpt: "투두리스트라도 자랑하고 싶어"

categories:
 - Backend
tags:
 - ['json-server', 'Heroku']
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---



## 참고 포스팅
[Hosting your backend API(JSON-server) on Heroku.](https://medium.com/cbazil-dev/hosting-your-backend-api-json-server-on-heroku-1a3b9b3d8f82)

참고 포스팅을 거의 똑같이 따라해 가는 글이다. 

## json-server
[https://github.com/typicode/json-server](https://github.com/typicode/json-server)  
json-server는 프론트 개발 연습할 때 가짜 API 서버로 활용하기 좋다. 로컬에서 테스트 해볼 때가 아닌 간단한 프로젝트라도 누군가에게 보여주려고 할 때 어떻게 하면 좋을 지 알아보다가 Heroku를 활용하는 방법을 찾았다.   
(굳이 이렇게 까지 해야 하나 싶을 수도 있지만 강의 따라서 만든 투두리스트앱 주변에 자랑합시다~) 

## 준비
기존에 json-server를 이용해 만든 프로젝트를 준비학고, [Heroku](https://www.heroku.com/) 계정이 없다면 회원가입.

## Heroku 새 앱 만들기
Heroku에 로그인하면 대시보드 화면이 나타나는데 오른쪽 위에 'New' > 'Create new app'을 눌러 새 앱을 만든다.

**App name**: 앱이름을 입력.  
**Choose a region**: United States

그리고 'Create app'을 클릭.  

앱 이름짓기 고민하다가 `to-do-list-hosting`이라고 했다. 

## Heroku CLI 설치
Heroku CLI가 설치되어 있지 않다면, [Heroku CLI 페이지](https://devcenter.heroku.com/articles/heroku-cli)를 참고해서 설치.  

## 호스팅할 폴더 준비
참고 포스팅을 보시면 알겠지만 템플릿 저장소를 다운 받는다. 

[https://github.com/Cbazil/vanilla-json-server-part-1](https://github.com/Cbazil/vanilla-json-server-part-1)

1. 이 폴더에서 `db.json` 파일만 기존의 프로젝트 폴더에서 쓰던 것으로 바꾸고.
2. 이 폴더에서 `npm install json-server`해서 json-server를 설치. 

## 호스팅
방금 다운받은 `db.json`이 있는 폴더에서 진행한다.  
### 1. 터미널에서 Heroku 로그인
```
heroku login
```
`q`를 누르면 취소되고 나머지 아무키나 누르면 기본 브라우저에서 로그인 창이 뜬다. 로그인하면 다시 터미널로 돌아온다.  
### 2. 깃 초기화
```
git init
```
### 3. 리모트 저장소 연결
```
heroku git:remote -a [heroku에서 설정한 앱 이름]
```
위에서 앱 이름을 `to-do-list-hosting`로 설정했기 때문에  
`heroku git:remote -a to-do-list-hosting` 이렇게 입력했다. 
### 4. 깃 커밋하고 푸시
```
git add .
git commit -am "hosting a json-server"
git push heroku main
```
### 5. Heroku에서 확인
Heroku로 돌아와서 해당 앱 페이지 오른쪽 위에 'Open app'을 클릭하면 업로드한 `db.json`내용을 확인할 수 있다.

## 기존 프로젝트 수정
기존에 json-server를 이용하던 프로젝트에서 API 요청 주소를 수정한다. 
`http://localhost:3000/...`로 쓰던 부분을 이제 `https://[heroku에서 설정한 앱 이름].herokuapp.com/...`으로 바꾸면 끝! 





## 문제 해결 기록
### CORS 에러?
사실 두 번째 해보는 거고 어려운 건 아니라서 금방 될 줄 알았는데 CORS에러가 떠서 한시간이나 방법을 찾아 헤맸다.  

[교차 출처 리소스 공유(CORS)](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)

결국 CORS가 중요한 건 아니었다. 포스팅을 천천히 다시 보면서 하나씩 확인했다.  
원인은 heroku에 올릴 데이터 폴더에서 json-server를 설치 안했기 때문이었다. json-server 설치하고 다시 git push를 하니 에러가 사라졌다. 
덕분에 거의 잊고 있었던 CORS에러에 대해 복습할 수 있었다. 

### SyntaxError: Unexpected token < in JSON at position 0
처음 보는 에러인데 일단 그대로 에러를 복붙해서 검색했다. 받아온 데이터가 JSON형식의 문자열이 아니어도 날 수 있는 에러라는 것이다.  
이런 저런 해결 방법을 보다가 다시 프로젝트로 돌아와 개발자 도구 Network를 한 번 살펴보기로 했다. fetch로 데이터를 받아오는 데에 에러는 없었다. 하지만 깨닫고 말았다. 애초에 요청을 보낸 API 주소를 잘못 썼다는 것을. 
```javascript
// 이렇게 써서 에러가 나고 있었다. 
const API_URL = "https://to-do-list-hosting.herokuapp.com"

// 이렇게 써야 했다.
const API_URL = "https://to-do-list-hosting.herokuapp.com/todos"
```
