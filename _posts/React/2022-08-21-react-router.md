---
title: "react router"
excerpt: "리액트에서 라우터 사용하기"

categories:
 - React
tags:
 - '스터디'
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---


## SPA가 나타난 배경

전통적인 방식의 웹 앱의 단점: 서버의 부담이 크다. 속도가 느려질 수 있다. 페이지 이동 시, 깜빡임이 발생한다. 

⇒ url을 이동하더라고 새로운 HTML을 서버에서 받지 말고 화면을 Client에서 그리자 (Client Side Rendering)

*라우팅: URL에 따라 알맞은 컨텐츠UI를 전달해주는 기능

### History_API

[https://developer.mozilla.org/ko/docs/Web/API/History_API](https://developer.mozilla.org/ko/docs/Web/API/History_API)

브라우저의 뒤로 가기, 앞으로 가기를 생각하면 된다. 
서버로 요청을 보내지 않고 페이지를 바꿀 수 있다. 브라우저의 세션 기록에 접근. 

## React-router 설치

[https://github.com/remix-run/react-router](https://github.com/remix-run/react-router)

react-router 는 react-router-dom(웹앱용) 과 react-router-native(모바일용) 을 포함한다. 웹앱만 만들것이기 때문에 react-router-dom(6버전)을 설치한다. 

`npm i react-router-dom@6`

6버전 이전과 조금 다르기 때문에 관련 글을 찾아볼 때 버전을 주의해야 한다. 

## a vs Link

a 태그는 새로운 html을 불러온다. (네트워크 탭에서 확인 가능) a 태그에 preventDefault 를 사용해서 구현할 수도 있지만 굳이 그렇지 않고 Link를 사용하면 된다. 

Link to 에는 문자열을 넣어야 한다.

```jsx
<Link to={`${doc.id}`} />
```

## Nested Routes 구성하기

Outlet은 중첩된 ui가 나타나도록 해준다. 

중첩된 ui가 없게 하고 싶으면 아웃렛을 사용하지 않으면 됨, 즉 중첩된 라우트가 아니라 형제라우트로 표시


```jsx
// with Outlet
<Route path={"react"} element={<ReactPage />}>
  <Route path=":docId" element={<ReactDocPage />} />
</Route>

// without Outlet
<Route path={"react"} element={<ReactPage />} />
<Route path="react/:docId" element={<ReactDocPage />} />
```


## useParams

```jsx
import { useParams } from "react-router-dom"

export default function ReactDocPage() {
  const params = useParams()
  console.log(params)

  return <div>ReactDocPage ##{params.docId}</div>
}
```

리액트 라우터을 사용할 때 파라미터의 정보를 가져와서 사용하고 싶을 때는 useParams를 사용할 수 있다. UseParams는 리액트 라우터 사용시 쓰는 거라 리액트 라우터 설치가 필요하다. 

UseParams는 키밸류로 이루어진 객체를 반환한다. 

블로그나 게시판 등의 포스트 리스트에서 특정 포스트를 눌렀을 때 파라미터로 넘어온 아이디 값을 확인하고 해당 아이디의 글을 보여줄 수 있다. 

## useNavigate 로 이동하기

내비게이션에서 클릭을 하면 해당 페이지로 이동하는 것을 구현할 때 사용할 수 있다. 

```jsx
import { useParams, useNavigate } from "react-router-dom"

export default function ReactDocPage() {
  const params = useParams()
  console.log(params)

  const navigate = useNavigate()

  return (
    <>
      <h5 onClick={() => navigate("/")}>logo</h5>
      <div>ReactDocPage ##{params.docId}</div>
    </>
  )
}
```

## Index Routes

해당되는게 없을 때 기본으로 보여지는 Path가 없는 라우트가 있다. 

부모 패스는 일치하지만 자식중에 패스가 일치하는 게 없을 때 보여지는 기본 자식 라우트라고 생각할 수 있다. 

```jsx
 <Routes>
  <Route path="/" element={<Layout />}>
    <Route index element={<Activity />} />
    <Route path="invoices" element={<Invoices />} />
    <Route path="activity" element={<Activity />} />
  </Route>
</Routes>
```

## Relative Links

Link로 자식요소 이동할 때 ‘/부모/자식' 이렇게 안쓰고 ‘자식'이렇게만 써도 된다. 즉 상대 경로를 사용할 수 있다. 

## Not Found Routes

```jsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="dashboard" element={<Dashboard />} />
  <Route path="*" element={<NotFound />} />
</Routes>
```

보여줘야 하는 페이지 별로 path와 element를 연결하고 마지막으로 어떤 path도 해당하지 않는 경우 보여줄 페이지를 설정할 수 있다. 

Path에는 `*`을 적고, 낫파운드일 때 보여줄 페이지를 연결한다.