---
title: "원티드 프리온보딩 인턴십 과제 회고"
excerpt: "원티드 인턴십 지원 후기 & todolist 구현 과제"

categories:
  - etc
  - React
tags:
  - 
# last_modified_at: 2022-07-10
toc: true

# published: false
---

과제 구현을 제대로 해본 적이 처음이다. 3달 전쯤 과제를 해 볼 기회가 있었는데 너무 어려워서 못 했다.. 

이번 과제는 간단한 로그인과 todolist 구현 과제이다. 

[>>>저장소>>>](https://github.com/kabinny/wanted-pre-onboarding-frontend)


## 구현 기능
### 추가한 라이브러리
- react-router-dom: 라우팅 처리
- axios: API 요청
- chota: 심플하고 최소한으로 스타일을 부여

### 로그인, 회원가입
#### 입력하는 이메일과 비밀번호의 유효성 검사
이메일은 `@`의 index가 0이상이면 통과하게 했다. 애초에 input type="email"이기 때문에 이메일 형식을 입력해야 한다. 비밀번호는 string.length를 확인해서 8자 미만일 때를 걸러냈다.  
유효성 검증을 통과하면 제출 버튼에 disabled를 제거한다.

#### 리다이렉트
회원가입이 성공하면 로그인 페이지로 이동한다.  
로그인 성공하면 투두 페이지로 이동한다. 
```javascript
const navigate = useNavigate()

const onSubmit = async (event: React.SyntheticEvent) => {
  event.preventDefault()
  const isValid = validateData(values) // 값 검증

  if (isValid) {
    try {
      signInAPI(values).then((res) => {
        localStorage.setItem("access_token", res.data.access_token)
        navigate("/todo")
      })
    } catch (e) {
      console.error(e)
    }
  }
}
```

#### 로그인 여부에 따른 리다이렉트 처리
-   로컬 스토리지에 토큰이 있는 상태로 `/signin` 또는 `/signup` 페이지에 접속한다면 `/todo` 경로로 리다이렉트 시켜주세요
-   로컬 스토리지에 토큰이 없는 상태로 `/todo`페이지에 접속한다면 `/signin` 경로로 리다이렉트 시켜주세요

Route들을 Route로 감싸고 그 안에서 토큰 확인하고 리다이렉트 혹은 Outlet 사용.  

**App.tsx**
```javascript
<BrowserRouter>
  <Routes>
    {/* 로그인 상태일 때 /todo 리다이렉트 */}
    <Route element={<RedirectRoutes />}>
      <Route path="/" element={<SignInPage />} />
      <Route path="/signin" element={<SignInPage />} />
      <Route path="/signup" element={<SignUpPage />} />
    </Route>

    {/* 로그인 필요 */}
    <Route element={<PrivateRoutes />}>
      <Route path="/todo" element={<TodoPage />} />
    </Route>
  </Routes>
</BrowserRouter>
```

**PrivateRoutes.tsx**
``` javascript
import { useEffect } from "react"
import { Outlet, useNavigate } from "react-router-dom"

function PrivateRoutes() {
  const isLoggedIn = localStorage.getItem("access_token")
  const navigate = useNavigate()

  useEffect(() => {
    if (!isLoggedIn) {
      navigate("/signin")
    }
  }, [])

  if (isLoggedIn) {
    return <Outlet />
  } else {
    return null
  }
}

export default PrivateRoutes
```

근데 다시 보니까 PrivateRoutes랑 RedirectRoutes를 하나로 합쳐도 될 거 같다. 프롭 추가해서 로그인 필요 페이지인지 아닌지 확인하면..


### 투두 리스트
#### 투두 생성
더 세심하게 할 수 있는 여지가 많다. 
form이 제출될 때, 
- 기본 새로고침 방지
- 투두 입력이 되었는지 확인
- 생성 요청 성공 응답이 오면 
	- 리스트 get 다시 하기
	- 투두 내용 state를 빈문자열로 초기화
	- 인풋 요소에 다시 포커스

**TodoForm.tsx**
```typescript
const onSubmit = async (event: React.SyntheticEvent) => {
  event.preventDefault()
  const isValid = value.todo.length > 0

  if (isValid) {
    try {
      createTodoAPI(value).then((res) => {
        getTodoList()
        setValue({ todo: "" })
        if (inputRef.current) {
          inputRef.current.focus()
        }
      })
    } catch (e) {
      console.error(e)
    }
  }
}
```


#### 투두 수정, 삭제
수정, 삭제를 할 때마다 투두리스트 get 다시 하기... 수정중 상태일때만 수정폼(인풋 텍스트와 수정 버튼)이 보인다.
- 체크박스로 완료 여부 수정
- 수정 버튼으로 투두 내용 수정
- 삭제 버튼으로 삭제 요청  

**TodoItem.tsx**
```typescript
function TodoItem({ id, todo, isCompleted, userId, getTodoList }: Props) {
  const [isModifying, setIsModifying] = useState(false)
  const inputModifyRef = useRef<HTMLInputElement>(null)

  const onCheck = () => {
    const value = {
      id,
      todo,
      isCompleted: !isCompleted,
    }
    try {
      updateTodoAPI(value).then((res) => {
      getTodoList()
    })
    } catch (e) {
      console.error(e)
    }
  }

  const deleteTodo = () => {
    try {
      deleteTodoAPI({ id }).then((res) => {
        getTodoList()
      })
    } catch (e) {
      console.error(e)
    }
  }

  return (
    <li style={{ marginBottom: "1rem" }}>
      <label>
        <input type="checkbox" onChange={onCheck} defaultChecked={isCompleted} />
        {!isModifying && <span style={{ padding: "0 1rem" }}>{todo}</span>}
      </label>

      {/* 수정 모드 */}
      {isModifying && (
        <TodoEditForm
          id={id}
          todo={todo}
          isCompleted={isCompleted}
          setIsModifying={setIsModifying}
          inputModifyRef={inputModifyRef}
          getTodoList={getTodoList}
        />
      )}

      {/* 기본 모드 */}
      {!isModifying && (
        <>
          <button
            type="button"
            className="button dark outline"
            onClick={() => {
              setIsModifying(true)
              setTimeout(() => {
                inputModifyRef.current?.focus()
              }, 0)
            }}
            data-testid="modify-button"
          >									
            수정
          </button>
          <button
            type="button"
            className="button error"
            onClick={deleteTodo}
            data-testid="delete-button"
          >					
            삭제
          </button>
        </>
      )}
    </li>
  )
}
```


## 스타일
UI 스타일은 중요하지 않으며 자연스럽게만 하면 된다고 했다. 가볍게 추가하고 싶었고 시간을 많이 쓰고 싶지 않아서 미니멀 css 라이브러리 찾아보다가 chota를 알게 되었다. 최종 고민했던 라이브러리들은 [mvp.css](https://andybrewer.github.io/mvp/), [chota](https://jenil.github.io/chota/), [miligram](https://milligram.io/) 이었다. mvp.css는 버튼이 취향이 아니었고, chota의 메인 컬러와 문서가 마음에 들어서 선택했다. 추가 스타일도 조금 필요했지만 깔끔하고 마음에 들게 나왔다. 

<img src="https://drive.google.com/uc?export=view&id=1uS48nvfKlkuBXCsbg-JJuRBapZO9Cb9F" alt="과제 구현" width="49%" />
<img src="https://drive.google.com/uc?export=view&id=1GRIPetGZqSgJeeFC1ejOGk3RjyRT-nlI" alt="과제 구현" width="49%" />

<img src="https://drive.google.com/uc?export=view&id=1BSggsQ92-iROaMN_EM3WLeiMtZ-tzslz" alt="과제 구현" width="49%" />
<img src="https://drive.google.com/uc?export=view&id=1Iev73M-OnROF32uLbmy0li6W5ktcODxg" alt="과제 구현" width="49%" />

<img src="https://drive.google.com/uc?export=view&id=1iiJwUVf5eGPi97OUm3UPj5haBk8JeTRC" alt="과제 구현"  width="49%" />
<img src="https://drive.google.com/uc?export=view&id=1XWEekSPJ3IGsjZIH0uMpH4loAdGGq9nj" alt="과제 구현"  width="49%" />

<img src="https://drive.google.com/uc?export=view&id=14nFHNGPrfTRRLyoDPWK_ZFcxpoBic__p" alt="과제 구현" width="49%" />
<img src="https://drive.google.com/uc?export=view&id=1rVJwO1iBxJgZfBRhpd98xxZYAmB2d9Gy" alt="과제 구현" width="49%" />

<img src="https://drive.google.com/uc?export=view&id=1S2KM3cBnsN4AqtUZ7LluKBG50ySwGnz-" alt="과제 구현" />










## 특징
### TypeScript...
타입스크립트 왕초보지만(interface만 조금 익숙한 상태), 간단한 투두리스트정도는 괜찮지 않을까 싶어서 도전했다. 목표는 any를 사용하지 않고 에디터에서 빨간 줄 안뜨기였다. 마침 원티드 프리온보딩 챌린지 2월 주제가 타입스크립트여서 타입스크립트를 고찰하고 있던 중이라 도움이 되었다. 그럼에도 아직 멀었다. 

### private router 구현하기
정말 부끄럽게도 작년 말에 했던 팀프로젝트에서는 접근 권한에 따른 라우팅 처리를 이번만큼 예쁘게 하지 못했다. 그때는 어떻게 해야할 지 감을 못잡아서 유저 권한 확인 후 리다이렉트 하는 것을 커스텀 훅으로 빼서 로그인 필요한 페이지 컴포넌트마다 갖다 박았다. 프라이빗 라우팅 구현에 실패한 것이 아니라 세련되지 못한 구현 방법을 하나 알게 된 것이다. 




## 개선점
### 삭제 기능
삭제 같은 중요한 거는 실수 방지를 위해 삭제 버튼 눌러도 삭제하시겠습니까라던가 한 번 더 확인하는 절차를 넣으면 좋겠다고 생각했다. 그런데 채점을 어떻게 하는 건지 모르고, 과제 구현 내용에도 없는 항목이라 넣지 않았다. 넣는다고 해도 그냥 `window.confirm`으로 하고 넘어갔을 수도 있겠다. 더 멋있는 방법을 시도해 보고 싶다. 

### 로그인 관련 에러 처리
조금 더 고민할 시간이 있었다면 회원가입/로그인 에러 오는 것을 처리하고 보여주고 싶었다. 콘솔 에러는 출력했지만 콘솔을 안보면 어떤 에러가 나고 있는지, 에러가 난 건지 알 수 없었기 때문이다. 그리고 타입스크립트 때문에 try catch 에서 `error.message`를 사용하려고 하면 추가로 해야할 게 있어서 그냥 `console.error(error)` 이렇게만 두었다. 

### 컴포넌트 분리
다시 보니까 input, button은 따로 공통 컴포넌트로 분리 했었어야 했다. 팀프로젝트 했던 시절 폴더 구조를 잘짰다고 극찬 받은 팀의 저장소를 받아서 참고해 봤는데 도움이 많이 되었다. 찬찬히 보니까 정말 완벽에 가까운 거 같아서 당분간은 진짜 많이 참고할 것 같다. 

### 전역 상태관리
정말 이렇게 기능이 적고 간단한 것을 만들면서도 전역 상태관리를 따로 해야되는데, 생각만 하고 적용하지는 못 했다. 전역 상태 관리는 필요성은 알지만, 구체적인 흐름과 사용법은 공부중이다ㅠ 



## 인턴십 지원 후기
### 에세이 작성
과제만 작성하면 되는 것이 아니고, 간단한 에세이도 작성해야 했다. 작성 가능한 블로그 서비스가 제시되어 있어서 이참에 처음으로 벨로그에 글을 써봤다. 
지원하고 싶은 참가기업, 인턴십에서 얻고 싶은 것, 합격까지 몇 개 이력서를 제출할 것인지 쓰는 것이다. 이게 은근 어려웠다. 신청 마지막날 마지막 시간까지 머리를 싸매고 썼다. 예쁘게 두괄식으로 쓰고 싶었는데 제출할 수 있는 분량 채우기 만으로도 조금 벅찼다. 다시 보니 부끄럽다. 
**최종 합격까지 100건 이상 넣겠습니다!** 

### 제출 후
github에서 과제 이름으로 검색하니까 거의 400개 쯤 되는 저장소가 나왔다. 그리고 몇몇 개를 살펴봤는데 정말 수준 높은 완성도여서 감탄을 엄청 했다. 인턴십 선발 과제에 이런 수준의 분들이 오시면 나는 어디로 가야 하는 걸까 얼마나 더 해야 하고 어디까지 해야 하는 걸까 수많은 생각이 머릿속을 스쳐갔다. 

아무튼 인턴십도 경쟁이 셀 거라고 생각해서 안되면 어떡하지 걱정도 많았다. 다행히 합격해서 기분은 좋았다. 사정상 포기하게 되었지만 커리큘럼이 지금 딱 필요한 거라서 다음에 기회가 된다면 꼭 참가해보고 싶다. 