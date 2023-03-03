---
title: "axios 인터셉터 활용하기"
excerpt: "axios 인터셉터를 사용해서 리프레시 토큰 요청 보내기"

categories:
 - JavaScript
tags:
 - axios
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

참고 Axios API > 인터셉터

[https://axios-http.com/kr/docs/interceptors](https://axios-http.com/kr/docs/interceptors)

## axios interceptors

강의 듣다가 구체적인 axios 장점 사용법을 알게 되었다. axios의 장점 중 하나는 요청을 보낼 때, 응답을 받을 때 중간에 가로채서 뭔가 할 수 있다는 것이다. 이론상 알고는 있었지만.. 나는 그동안 axios를 fetch 대용으로만 사용했었는데 뭣하러 그랬던 것인가. 심지어 axios는 fetch를 사용하는 라이브러리도 아닌데… (axios는 XMLHttpRequests를 사용한다, fetch를 사용하는 건 [ky](https://github.com/sindresorhus/ky))

예를 들어 요청 보낼 때, header에 공통으로 들어갈 것들을 넣어준다던지(요청을 보내는 쪽에서 일일이 넣지 않아도 되게), 응답 받을 때 특정 에러인 경우 다른 요청을 보낸다던지(로그인 토큰 만료되었다는 에러일 때 리프레시 요청 등) 할 수 있다. 

## axios 개조하기

src/services/axios/authAxios.js

```jsx
import axios from "axios"
import store from "@/store" // accessToken을 store에 저장중이라서..
import { refreshToken } from "../login" // refreshToken 요청하는 것 불러오기

const authAxios = axios.create() // 커스텀 인스턴스 만든다. 

// 기본 baseURL 설정 해두면 사용하는 쪽에는 '/'부터 쓰면 된다. 
// 예) authAxios.get("/refreshToken")
authAxios.defaults.baseURL = "http://localhost:3000/"

// 요청 인터셉터 추가하기
authAxios.interceptors.request.use(
  function (config) {
    // 요청이 전달되기 전에 필요한 작업 수행
    // 여기서 헤더에 토큰을 넣어주기 때문에 호출할 때 토큰을 넣지 않아도 된다.
    config.headers["access-token"] = store.state.accessToken
    config.headers["refresh-token"] = store.state.refreshToken
    return config
  },
  function (error) {
    // 요청 오류가 있는 작업 수행
    return Promise.reject(error)
  }
)

// 응답 인터셉터 추가하기
authAxios.interceptors.response.use(
  function (response) {
    // 2xx 범위에 있는 상태 코드는 이 함수를 트리거 합니다.
    // 응답 데이터가 있는 작업 수행
    return response
  },
  async function (error) {
    // 2xx 외의 범위에 있는 상태 코드는 이 함수를 트리거 합니다.
    // 응답 오류가 있는 작업 수행

    const errorAPI = error.config
    
    // 토큰 유효기간 지난 에러를 401로 보냄
    // retry는 한 번만 요청을 보내기 위해서 사용
    // 401에러이고 처음 요청일 때, 리프레시 토큰을 요청한다. 
    if (error.response.status === 401 && errorAPI.retry === undefined) {
      errorAPI.retry = true // 여러번 요청했을 때 무시될 수 있게
      await refreshToken()
      return await authAxios(errorAPI)
    }
    return Promise.reject(error)
  }
)

export default authAxios
```

## authAxios 사용하기

src/services/login.js

```jsx
import authAxios from "./axios/authAxios" // 아까 만든 것을 불러옴
import store from "@/store"

export const refreshToken = async () => {
  try {
    const { data } = await authAxios.get("/refreshToken")
    store.commit("setAccessToken", data.accessToken)
  } catch (err) {
    console.log(err)
  }
}
```

## 마무리

express로 토큰 발급해주는 서버를 만들어서 테스트 해보니까 리프레시 토큰을 조금 더 이해하게 되었다. axios 인터셉터를 이용하는 것이 토큰 만료 응답이 왔을 때, 리프레시 요청하는 더 깔끔한 방법인 것 같다.