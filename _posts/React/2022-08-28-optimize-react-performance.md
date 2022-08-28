---
title: "React 렌더링 성능 최적화"
excerpt: "useMemo, useCallback, React.memo, Code Splitting"

categories:
 - React
tags:
 - '스터디'
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

## 컴포넌트가 렌더링 될 때
컴포넌트는 자신의 state가 변경, props가 변경 될 때마다 다시 렌더링 된다.  
함수 컴포넌트가 렌더링 된다는 것은 해당 함수가 다시 호출된다는 것, 즉 그 안에 있는 값 들이 다시 계산 된다는 것이다. 앱이 작을 때는 계속 컴포넌트가 렌더링 되는 것이 상관 없을 수 있지만, 앱이 커질 수록 렌더링 최적화 방법이 필요해진다. 


## useMemo와 useCallback

의존성이 변경되었을 때만 함수 다시 계산한다. 고비용 계산인 경우인 경우에만 useMemo를 쓰면 좋다.  

useMemo: 메모이제이션된 **값**을 반환. 성능 최적화를 위해 사용할 수 있지만, 반드시 보장되지는 않는다. 

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

useCallback: 메모이제이션된 **콜백 함수**를 반환.

```jsx
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

### 메모이제이션
메모이제이션은 피보나치 수열 재귀로 구할 때 접했던 개념이었다. 동일한 계산을 반복해야 할 때, 이전에 계산한 값을 메모리에 저장함으로써 동일 계산을 반복하지 않는 기술이다. 

## React.memo

고차 컴포넌트(Higher Order Component).

컴포넌트를 래핑한다(함수 컴포넌트를 React.memo로 감싼다).

컴포넌트를 렌더링하지 않고 마지막으로 렌더링된 결과를 재사용. props변화에만 영향을 준다. 렌더링 방지 목적으로 사용하면 안되고 성능 최적화만 위해 사용해야 한다. 

```jsx
const MyComponent = function MyComponent(props) {
  /* props를 사용하여 렌더링 */
};

// 이렇게 변경
const MyComponent = React.memo(function MyComponent(props) {
  /* props를 사용하여 렌더링 */
});
```

## Code Splitting

spa 최적화 방법 중 하나. 



보통은 한 번에 번들된 전체 코드를 로드하지만, 앱이 커지면 번들이 커진다. 번들을 나누고 지연로딩되게 하는 방법으로 성능 최적화를 할 수 있다.

**Dynamic import**
```jsx
// 함수를 다이나믹 임포트
import("./math").then((math) => console.log(math.add(1, 2)))
```

**React.lazy()**
컴포넌트를 동적 임포트 하려면 React.lazy() 사용. Suspense로 감싸주고 사용한다. 

```jsx
import React, { useState, lazy, Suspense } from "react"

const Post = lazy(() => import("./Post"))
```

일반적으로 route-based 로 코드를 나눈다.