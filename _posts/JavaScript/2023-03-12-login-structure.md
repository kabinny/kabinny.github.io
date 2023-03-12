---
title: "로그인의 개념과 어플리케이션 구조 알아보기"
excerpt: "원티드 프리온보딩 챌린지 3월 1강"

categories:
 - JavaScript
tags:
#  - TypeScript
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

[3월 챌린지 링크](https://www.wanted.co.kr/events/pre_challenge_fe_7)

프론트엔드 프리온보딩 챌린지는 1월부터 들었다. 1월은 너무 어려워서 멘탈이 멀리 나갔었고, 2월은 난이도가 딱이었는데 중간부터 길을 잃어서(?) 차분히 정리할 수 없었다.. 3월 부터는 해보려고. 

React + TypeScript 


## 로그인이란
접근 허가 증명을 얻기 위해.. 시스템에 접근하기 위한 작업(?)

## 로그인 구현을 위한 개념
- 사용자 식별
- 접근 및 동작 제어 (사용자에 따라 접근할 수 있는 것, 할 수 있는 것이 다를 수 있다)
- 권한이 없는 자원에 접근하지 않는 구조 만들기 (존재조차 모르게..)
- 로그인, 로그아웃 기능
- 인증 정보 관리 (세션 스토리지에? 쿠키에? 로컬스토리지???)

### 프론트에서 필요한 최소 구현
- 로그인 페이지
- 로그인 인증관련 데이터 관리
- 로그인 상태에 따른 화면/기능 제어
- 로그아웃 기능



## 로그인 페이지 코드 구조
답안지를 보고 공부하는 자세로 내가 이해한 설명을 추가해 봤다. 


### type, mock data
```typescript
import React, { useState } from "react"

// 로그인 응답의 메세지 타입
type LoginSuccessMessage = "SUCCESS"
type LoginFailMessage = "FAIL"

// 로그인 응답 타입
interface LoginResponse {
  message: LoginSuccessMessage | LoginFailMessage
  token: string
}

// 보여질 유저 정보 (로그인에 필요한 이름, 비밀번호 제외하고)
interface UserInfo {
  name: string
}

// 유저 타입
interface User {
  username: string
  password: string
  userInfo: UserInfo
}

// 유저 리스트
const users: User[] = [
  {
    username: "blue",
    password: "1234",
    userInfo: { name: "blueStragglr" },
  },
  {
    username: "white",
    password: "1234",
    userInfo: { name: "whiteDwarf" },
  },
  {
    username: "red",
    password: "1234",
    userInfo: { name: "redGiant" },
  },
]

// 토큰 키로 사용될 임의 문자열
const _secret: string = "1234qwer!@#$"
```



### 함수 정의
```typescript
// 로그인 함수
// 이름과 비밀번호를 파라미터로 받고, 프로미스 반환
// (async 함수는 프로미스를 반환)
const login = async (username: string, password: string): Promise<LoginResponse | null> => {
  // 존재하는 유저이고 비밀번호도 일치하면 해당 요소 반환
  const user: User | undefined = users.find((user: User) => {
    return user.username === username && user.password === password
  })
  // user가 존재하면 (user가 undefined가 아닌 경우) 성공 메세지와 토큰(문자열)을 반환
  return user
    ? { message: "SUCCESS", token: JSON.stringify({ user: user.userInfo, secret: _secret }) }
    : null
}

// 유저 정보 조회 함수
// 토큰을 파라미터로 받고, 프로미스 반환
const getUserInfo = async (token: string): Promise<UserInfo | null> => {
  // 문자열 토큰을 파싱
  const parsedToken = JSON.parse(token)
  // secret이 없거나, _secret과 일치하지 않는 경우 null 반환
  if (!parsedToken?.secret || parsedToken.secret !== _secret) return null

  // 로그인된 유저 찾기. 유저 리스트에서 이름이 일치하는 유저
  const loggedUser: User | undefined = users.find((user: User) => {
    if (user.userInfo.name === parsedToken.user.name) return user
  })

  // 로그인된 유저가 있으면 해당 유저의 userInfo 반환
  return loggedUser ? loggedUser.userInfo : null
}
```


### 페이지 컴포넌트
```typescript
// 페이지 컴포넌트
const LoginWithMockAPI = () => {
  const [userInfo, setUserInfo] = useState<UserInfo>({ name: "" })

  // 로그인 폼 제출되었을 때 실행
  const loginSubmitHandler = async (event: React.FormEvent<HTMLFormElement>) => {
    // 폼 제출시 기본 새로고침을 방지
    event.preventDefault()

    // 폼데이터 생성
    const formData = new FormData(event.currentTarget)

    // 입력된 이름과 비밀번호로 로그인 시도
    const loginRes = await login(
      formData.get("username") as string,
      formData.get("password") as string
    )
    // 로그인 결과가 null인 경우 종료
    if (!loginRes) return

    // 로그인 성공시 받아온 토큰으로 유저 정보를 조회
    const userInfo = await getUserInfo(loginRes.token)
    // 유저 정보가 null인 경우 종료
    if (!userInfo) return

    // 화면에 보이는 유저 정보 갱신
    setUserInfo(userInfo)
  }

  return (
    <div>
      <h1>Login with Mock API</h1>
      {/* 폼이 제출되면 loginSubmitHandler 실행 */}
      <form onSubmit={loginSubmitHandler}>
        <label>
          Username:
          <input type="text" name="username" />
        </label>
        <label>
          Password:
          <input type="password" name="password" />
        </label>
        <button type="submit" value="Submit">
          submit
        </button>
      </form>
      <div>
        <h2>User info</h2>
        {/* userInfo는 객체이기 때문에 문자열로 바꿔서 보여줌 */}
        {JSON.stringify(userInfo)}
      </div>
    </div>
  )
}

export default LoginWithMockAPI

```

## 마무리
이번 시간에 메인 구현 내용은 아니었지만, 페이지마다 로그인 여부 확인하는 부분을 모듈화하는 부분도 나왔는데 흥미로웠다. 어떻게 도달하는 걸까.. 