---
title: "Lifecycle, useEffect"
excerpt: "생명주기, 클래스형 컴포넌트와 함수형 컴포넌트, useEffect"

categories:
 - React
tags:
 - '스터디'
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---


리액트는 예전에 개념 정도만 공부했다가 제대로 공부한 지는 얼마 안 되었지만 어렵다. 

특히 초반에 헷갈렸던 부분이 생명주기 메서드와 리액트 훅이었다. 언제 어떻게 쓰이는지, 둘이 같이 쓰는 건지 이런 부분이 많이 헷갈렸었다. 

## 클래스형 컴포넌트와 함수형 컴포넌트

리액트에서는 예전에 클래스형 컴포넌트 방식을 사용하다가 함수형 컴포넌트가 나중에 나왔다. 어쩐지 최근의 리액트 관련 글을 찾아보면 클래스형 컴포넌트에 대한 내용이 거의 없다. 

- 훅(hook)은 **함수형** 컴포넌트를 제어할 때 쓰인다.
- 생명주기 메서드는 **클래스형** 컴포넌트를 제어할 때 쓰인다.

현재는 클래스형 컴포넌트를 사용하지 않는다고 보면 되기 때문에 생명주기 메서드도 사용할 일이 없다고 보면 된다. 하지만 언제 예전 리액트 코드를 마주칠지 모르니 알아두면 좋다. 

## Lifecycle

리액트의 라이프 사이클을 다이어그램으로 표현해주는 사이트 

[https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

![리액트의 라이프 사이클](https://drive.google.com/uc?export=view&id=1sK6Zx_94PN3Fa4z08WNaCdNP7t3P7TgL)

컴포넌트의 생명주기는 컴포넌트가 만들어 지고, 돔에 연결되고, 수정되고(업데이트), 사라지는 과정이라고 보면 된다. 

생명주기 메서드를 사용하면 컴포넌트가 생성될 때, 컴포넌트가 마운트될 때, 컴포넌트가 업데이트될 때, 컴포넌트가 언마운트될 때 등에 실행되어야 할 코드를 작성할 수 있다. 

리액트보다 뷰를 먼저 접해서 리액트의 클래스형 컴포넌트를 봤을 때 뷰와 비슷한 부분이 많았다. 

[리액트와 뷰 비교 코드가 잘 정리된 포스트](https://ryuhojin.tistory.com/16)

## useEffect

```jsx
import { useEffect } from "react" // useEffect 불러오기

useEffect(() => {
  // 작동할 코드
}, [state, props.a]) // 의존성 배열
```

useEffect는 리액트에서 자주 사용하는 기본 hook 중에 하나다. 

위의 라이프 사이클 메서드와 비교하면 componentDidMount, 특정 값이 변경 되었을 때만 해당하는 componentDidUpdate처럼 동작한다. 

두 번째 인자로 들어가는 저 배열을 의존성 배열(Array dependencies)이라고 부른다. 

- useEffect(effect): 의존성 배열을 안넣으면 마운트 되고, 업데이트 될 때 실행
- useEffect(ettect, []): 빈배열 넣으면 마운트 될 때만 실행
- useEffect(effect, [state]): 마운트 될 때, 배열에 넣은 값이 변경될 때 실행

```jsx
import { useEffect } from "react"

useEffect(() => {
  return () => {
    // cleanup
  }
}, [])
```

useEffect 내에서 함수를 리턴하면, 컴포넌트가 언마운트 될 때 실행된다. 라이프 사이클 메서드의 componentWillUnmount처럼 동작한다. 필수로 입력해야 하는 것은 아니다. 

useEffect는 하나의 컴포넌트 안에서 여러 개 사용할 수 있다. 관심사를 구분하기 위해 나눠서 작성한다. 

## 마무리

컴포넌트의 생명주기 개념은 알아두면 좋은 것 같다.  

useEffect 의존성 배열에 값이 있으면 그 값이 변경될 때마다 호출된다. 그래서 배열은 있지만 값이 없을 때, 다시 호출되려면 값이 변경되기를 기다려야 하는데 감시(?)할 값이 없기 때문에 마운트 될 때 한 번만 실행된다고 생각하면 편할 듯 하다. 배열을 넣는 것 자체가 이 값들을 감시할 것입니다-라고 명시한다고 보면 되지 않을까. 그래서 배열을 넣지 않으면 의존할 값(감시할 값)이 없기 때문에 컴포넌트가 렌더링 될 때마다 호출이 된다고 생각하기로 했다. 