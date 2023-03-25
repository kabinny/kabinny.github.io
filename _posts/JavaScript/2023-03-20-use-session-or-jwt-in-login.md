---
title: "세션기반 로그인 구현과 JWT와의 비교 및 인프라스트럭쳐"
excerpt: "원티드 프리온보딩 챌린지 3월 3강"

categories:
 - JavaScript
tags:
- JWT
- Session
#  - TypeScript
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

## 세션이란
- 실체가 있는 것이 아님.. 사용자가 로그인하고 로그아웃, 만료까지의 기간.
- 서버에서 브라우저를 표시한다. => 서버에서 세션 아이디를 기록해 두고 인증에 사용
- 쿠키를 사용해서 세션아이디(sid)를 기록

## 쿠키란
서버가 브라우저로 전송하는 데이터 조각. 동일한 서버에 재 요청 시 저장된 쿠키 데이터들 함께 전송. stateless한 (상태가 없는) HTTP에서 상태 정보를 기억시켜 준다!

쿠키는 요청 보낼 때 직접 넣어주는 것이 아니고 자동으로 넣어진다. [건드리지 마세요 리스트](https://developer.mozilla.org/en-US/docs/Glossary/Forbidden_header_name)에 있기 때문에 헤더에 넣으려고 하지 말자. 

## 쿠키 관련 정책
쿠키를 아무에게나 뿌리면 안되기 때문에 보안 관련 정책을 지정한다. 

### SameSite
같은 도메인에서만 저장하고 전송할 것인가?
- None: 아무데나 보낸다..
- Lax: 대부분 Strict 하지만 일부 안전한 요청(GET같은..)에는 허용
- Strict: 동일 출처만 허용

### httpOnly
- true: js로 접근 불가하게, http 요청으로만 세팅할 수 있게 한다.

### Secure
- true: https 요청을 통해서만 왔다갔다 할 수 있게 하는 옵션

## CORS
교차 출처 리소스 공유 정책 - 최초 자원이 서비스된 도메인 밖의 다른 도메인으로부터 요청할 수 있게 허용하는 구조. 동일 출처 보안 정책(same-origin policy)에 의해 기본적으로 금지된다. 

### 오류 해결 방법
- 크롬을 cors 오류 무시모드로 열어서 테스트(Mac)
```shell
open -n -a /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --args --user-data-dir="/tmp/chrome_dev_test" --disable-web-security
```

- (백엔드) origin, methods를 지정
- (테스트 용으로) 크롬에서 [Allow CORS](https://chrome.google.com/webstore/detail/allow-cors-access-control/lhobafahddgcelffkeicbaginigeejlf) 등의 확장 프로그램을 사용한다. 




## JWT와 세션의 장단점

|  | JWT | 세션 |  
| ----------- | ----------- | ----------- |  
| 장점 | 서버 비용 감소 | 프론트엔드 인증 쉬움, 보안 향상 |  
| 단점 | 프론트엔드 복잡도 증가, 세션보다 보안 위험 | 서버 비용 증가 |

서비스의 상황에 따라서 선택.. 


## 프로젝트 디렉토리 구조
정답은 없다~
디렉토리를 왜 정리해야 하는가.
생산성을 위해, 필요한 파일을 잘 찾기 위해

=> 모두가 마음에 드는 방법으로, 협의되고, 개발하기 좋다면 문제 없음.

**기능상 이슈가 없는 선에서**  
리액트 앱의 public 폴더나 next 앱의 pages 폴더는 번들러가 집어가는 방식이 다르기 때문에 주의가 필요함(번들러 설정을 여차저차 하면 되겠지만 굳이..?)

