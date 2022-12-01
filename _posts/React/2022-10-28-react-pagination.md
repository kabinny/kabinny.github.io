---
title: "React에서 페이지네이션 구현하기"
excerpt: "라이브러리 없이 구현"

categories:
 - React
tags:
 - 
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

페이지네이션을 쉽게 생성해주는 좋은 라이브러리도 많지만 직접 구현해 보기로 했다. 

## 구현 기능

- 버튼 구성: 이전으로 버튼, 페이지 숫자 버튼들, 다음으로 버튼
- 페이지 숫자는 (현재 페이지 - 4) 부터 (현재 페이지 + 4) 까지 총 9개(최대)가 보이는 형태
- 페이지 숫자 클릭하면 현재 페이지가 해당 페이지로 변경
- 현재 페이지가 첫 페이지면 이전으로 버튼 비활성화
- 현재 페이지가 마지막 페이지면 다음으로 버튼 비활성화
- 현재 페이지가 바뀌면 URL parameter 도 바꿔주기

## 구현 코드

### 리스트 컴포넌트

- react-router-dom 설치가 필요.
- 현재 페이지는 useSearchParams를 이용해 받아온다. 값이 없으면 기본 1페이지이다.
- useSearchParams 가져오는 값이 기본 문자열이기 때문에 parseInt를 사용해 정수로 바꿔줬다.
- 페이지네이션 컴포넌트로 최대 페이지(maxPage), 현재페이지(currentPage), 클릭시 실행될 함수(onClickPageButton)을 넘긴다.


```jsx
import { useEffect, useState } from "react"
import { useSearchParams } from "react-router-dom"
import ListPagination from "../components/.../ListPagination"

function ListPage() {
  // 페이지네이션 관련
  const [searchParams, setSearchParams] = useSearchParams()
  const [myListMaxPage, setMyListMaxPage] = useState(1)
  const myListPage = parseInt(searchParams.get("mylistpage") ?? "1", 10)

  function updateParams(updates) {
    setSearchParams({
      mylistpage: parseInt(searchParams.get("mylistpage") ?? "1", 10),
      ...updates,
    })
  }

  useEffect(() => {
    // 리스트를 받아온다.
    setMyListMaxPage(/* 최대 페이지 저장 */)
  }, [])

  return (
    <>
      <div>리스트 아이템들이 보이는 부분</div>
      <ListPagination
        maxPage={myListMaxPage}
        currentPage={myListPage}
        onClickPageButton={(pageNumber) =>
          updateParams({ mylistpage: pageNumber })
        }
      />
    </>
  )
}

export default ListPage
```

### 페이지네이션 컴포넌트

- 버튼 컴포넌트(PageButton)는 반복되기 때문에 따로 빼두었지만 짧아서 파일 분리까지는 하지 않았다.
- 숫자 버튼은 현재 페이지를 기준으로 이전 4개, 이후 4개 범위만 나타나도록 했다. 

```jsx
function ListPagination({ currentPage, maxPage, onClickPageButton }) {
  return (
    <div>
      {/* 이전 버튼 */}
      <button
        disabled={currentPage === 1 || maxPage === 0}
        onClick={() => {
          onClickPageButton(currentPage - 1)
        }}
      >
        이전
      </button>

      {/* 페이지 버튼 최대 9개가 보임 */}
      {new Array(9).fill(null).map((_, index) => {
        if (
          currentPage - (4 - index) >= 1 &&
          currentPage - (4 - index) <= maxPage
        ) {
          return (
            <PageButton
              key={currentPage - (4 - index)}
              number={currentPage - (4 - index)}
              onClick={onClickPageButton}
              selected={currentPage === currentPage - (4 - index)}
            />
          )
        }
      })}

      {/* 다음 버튼 */}
      <button
        disabled={currentPage === maxPage || maxPage === 0}
        onClick={() => {
          onClickPageButton(currentPage + 1)
        }}
      >
        다음
      </button>
    </div>
  )
}

// 버튼 컴포넌트
function PageButton({ number, onClick, selected }) {
  return (
    <button
      className={`${selected && "선택되었을 때 스타일 클래스"}`}
      onClick={() => onClick(number)}
    >
      {number}
    </button>
  )
}

export default ListPagination
```

## 개선점

- 페이지 숫자 보이는 개수도 넘겨 받아서 사용할 수 있게 하면 더 유동적으로 쓰일 수 있을 것 같다.
- 첫페이지와 마지막 페이지로 이동하는 버튼이 추가로 있어도 좋을 것 같고, 이 버튼이 나타나는지 여부도 프롭스로 넘겨받을 수 있겠다.