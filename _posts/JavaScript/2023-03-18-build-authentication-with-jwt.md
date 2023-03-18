---
title: "JWT의 개념과 토큰을 이용한 로그인 구현 알아보기"
excerpt: "원티드 프리온보딩 챌린지 3월 2강"

categories:
 - JavaScript
tags:
- JWT
#  - TypeScript
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---



## 토큰이란
허용되지 않은 누군가가 보면 안되는 프라이빗 정보를 보는 것을 막기위해 사용되는 데이터 쪼가리.  
=> 신원을 증명하는 것

## JWT
JSON Web Token
토큰, 토큰을 만드는 기술.

[https://jwt.io/](https://jwt.io/)


### JWT 내부
HEADER.PAYLOAD.SIGNATURE


**HEADER**
- 암호화 규칙(해싱 알고리즘)
- 토큰 타입   
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**PAYLOAD**
- 데이터  
```
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```

**SIGNATURE**
- 암호화를 위한 데이터  
```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  시크릿키..
) secret base64 encoded
```

## 보안 문제
- payload에 password같은 중요한 값이 있는 경우
- 토큰 탈취..(어디에 어떻게 저장해야 하는가.)
	- 런타임 메모리에 저장: 접근할 수는 없지만, 매번 로그인을 해야함..
	- 로컬 스토리지, 쿠키에 저장시 XSS문제: 값을 볼 수도 있고, 스크립트 주입될 수도 있음..
	- 쿠키에 저장시 CSRF문제: 악성 사이트에서 쿠키를 전송할 수 있음

## Refresh Token 사용하기
리프레시 토큰을 이용하지 않고 액세스 토큰만 이용했을 때 문제는, 액세스 토큰을 탈취당했을 때 그 토큰만 있어도 누구나 사용할 수 있는 것이다.  
인증할 때 리프레시 토큰과 액세스 토큰을 둘 다 발급한다. 액세스 토큰은 짧은 시간만 유효하게 로컬에 발급하고, 리프레시 토큰은 HttpOnly Cookie로 발급을 한다. 액세스 토큰도 HttpOnly Cookie에 넣어버리면 같이 털리기 때문이다.  
CSRF 공격으로 리프레시 토큰을 재사용 하려고 하면, 서버에서 액세스 토큰 아직 살아 있는데(유효기간 안끝났는데) 왜 리프레시 토큰을 쓰니, 너네 둘 다 무효야 로그인부터 다시해라고 한다. (액세스 토큰이 죽은 상태에서 리프레시 토큰을 쓰는 것은 어쩔 수 없다고 한다. 완벽한 보안 방법은 없기 때문에..)


## 코드 구조
### src/api/login.ts
```typescript
// 폼데이터로 로그인
export const login = async (args: LoginRequest): Promise<LoginResult> => {

  // 로그인 결과
  const loginRes = await fetch(`${BASE_URL}/auth/login`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(args),
  })

  // 로그인이 성공하면
  if (loginRes.ok) {
    // 로그인 결과 데이터를 (액세스 토큰이 있음)
    const loginResponseData = await loginRes.json()
    // 로컬스토리지에 저장하고
    saveAccessTokenToLocalStorage(loginResponseData.access_token)
    // "success" 반환
    return "success"
  }

  // 로그인 실패인 경우 "fail" 반환
  return "fail"
}

// 유저 정보 가져오기
// 파라미터 없음 <= fetchClient에서 로컬스토리지에 저장된 정보(액세스 토큰)를 가져와서 사용하기 때문에
export const getCurrentUserInfo = async (): Promise<UserInfo | null> => {
  // 유저 정보 GET
  const userInfoRes = await fetchClient(`${BASE_URL}/profile`, { method: "GET" })

  // fetch 성공이면 해당 정보 반환
  if (userInfoRes.ok) {
    return userInfoRes.json() as Promise<UserInfo>
  } 

  // fetch 실패면 null 반환
  return null
}
```

### src/pages/JWTLoginWidhLocalStorage.tsx
```typescript
import React, { useState } from "react"
import { getCurrentUserInfo, login } from "../../api/login"
import { UserInfo } from "../../types/user"

// JWT 로그인
const JWTLoginWithLocalStorage = () => {
const [userInfo, setUserInfo] = useState<UserInfo | null>(null)

// 로그인 폼 제출했을 때
const loginSubmitHandler = async (event: React.FormEvent<HTMLFormElement>) => {
  // 기본 새로고침 방지
  event.preventDefault()

  // 폼데이터 가져오기
  const formData = new FormData(event.currentTarget)

  // 폼데이터로 로그인 시도, login의 반환 값은 "success" 혹은 "fail"
  const loginResult = await login({
    username: formData.get("username") as string,
    password: formData.get("password") as string,
  })

  // 로그인 실패인 경우 종료
  if (loginResult === "fail") return

  // 로그인 성공인 경우 유저 정보를 가져온다
  // 반환값은 Promise<UserInfo 혹은 null
  const userInfo = await getCurrentUserInfo()

  // 유저 정보 가져오기 실패인 경우 종료
  if (userInfo === null) return

  // 유저 정보 값을 갱신
  setUserInfo(userInfo)
}

  return (
    <div>
      <h1>Login with JWT - localstorage</h1>
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
      {JSON.stringify(userInfo)}
      </div>
    </div>
  )
}

export default JWTLoginWithLocalStorage
```