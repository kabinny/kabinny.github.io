---
title: "stompjs 2 → 5버전에 맞춰 리액트 코드 작성하기"
excerpt: "공식 문서를 잘 읽자."

categories:
 - JavaScript
 - React
tags:
 - stompjs
 - react
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

## 문제 상황

백엔드 개발자 분이 Java프로젝트 폴더 안에 직접 뷰 부분 코드를 넣어서 했을 때는 채팅테스트가 잘 되었다. 그런데 내가 그 코드를 보고 옮겨적었더니 채팅 테스트가 잘 안되었다. 참고했다는 블로그 글을 앞뒤로 열심히 읽어 봤지만 백엔드 기반 설명이라서 어떤 상황인지 잘 이해가 안됐다. 

## 원인

블로그 글을 자세히 보니 예시 코드가 stompjs 2버전으로 되어 있었다. 그리고 내가 npm으로 설치한 버전은 6번대였다. 

2버전으로 설치하면 될까 했지만 에러가 나서 다시 돌렸다.

(참고했던 블로그 코드)

```html
<script src="https://cdn.jsdelivr.net/npm/sockjs-client@1/dist/sockjs.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/stomp.js/2.3.3/stomp.min.js"></script>
```

```jsx
  const chats = document.querySelector('.chats');
  const messageContent = document.querySelector('#message');
  const btnSend = document.querySelector('.btn-send');

  const chatRoomId = [[${chatRoomId}]];
  const nickname = [[${nickname}]];

  const sockJS = new SockJS("/stomp/chat");
  const stomp = Stomp.over(sockJS);

  stomp.heartbeat.outgoing = 0; //Rabbit에선 heartbeat 안먹힌다고 함
  stomp.heartbeat.incoming = 0; //Rabbit에선 heartbeat 안먹힌다고 함

  function onError(e) {
      console.log("STOMP ERROR", e);
  }

  function onDebug(m) {
      console.log("STOMP DEBUG", m);
  }

  stomp.debug = onDebug;

  stomp.connect('guest', 'guest', function (frame) {

      console.log('STOMP Connected');
      
      /* subscribe 설정에 따라 rabbit의 Exchange, Queue가 상당히 많이 바뀜 */
      stomp.subscribe(``, function (content) {

          const payload = JSON.parse(content.body);

          let className = payload.nickname == nickname? 'mine' : 'yours';

          const html = `<div class="${className}">
                          <div class="nickname">${payload.nickname}</div>
                          <div class="message">${payload.message}</div>
                      </div>`

          chats.insertAdjacentHTML('beforeend', html);
          
          //밑의 인자는 Queue 생성 시 주는 옵션
          //auto-delete : Consumer가 없으면 스스로 삭제되는 Queue
          //durable : 서버와 연결이 끊겨도 메세지를 저장하고 있음
          //exclusive : 동일한 이름의 Queue 생길 수 있음
      },{'auto-delete':true, 'durable':false, 'exclusive':false});

      //입장 메세지 전송
      stomp.send(`/pub/chat.enter.${chatRoomId}`, {}, JSON.stringify({
          memberId: 1,
          nickname: nickname
      }));

  }, onError, '/');

  //메세지 전송 버튼 click
  btnSend.addEventListener('click', (e) => {
      e.preventDefault();

      const message = messageContent.value;
      messageContent.value = '';

      stomp.send(`/pub/chat.message.${chatRoomId}`, {}, JSON.stringify({
          message: message,
          memberId: 1,
          nickname: nickname
      }));
  });

```

## 해결 방법

여러 블로그 글을 봐도 잘 모르겠어서 공식 문서를 천천히 읽어보기로 했다. 결론은 공식 문서에서 굉장히 필요한 부분만 잘 설명해 주고 있었고 실제로 적용하는 데도 어렵지 않았다. 

### 큰 차이점

**1. sockJS 연결 부분**
```jsx
// ver 2
const sockJS = new SockJS("/stomp/chat");

// ver 5+
stompClient.webSocketFactory = function () {
  return new SockJS("http://채팅서버:8080/stomp/chat")
}
```


**2. 메세지 전송 send ⇒ publish**
```jsx
// ver 2
stomp.send(`/pub/chat.message.${chatRoomId}`, {}, JSON.stringify({
    message: message,
    memberId: 1,
    nickname: nickname
}))

// ver 5+
stompClient.publish({
  destination: `/pub/chat.message.${roomId}`,
  body: JSON.stringify({
    message: input.value,
    userId,
    profileImage: "{유저 프로필 이미지 주소}",
    senderName: "{유저 닉네임}",
  })
})
```


**3. stompclient 생성**
```jsx
// ver 2
const stomp = Stomp.over(sockJS)

// ver 5+
const stompClient = new StompJs.Client({
  brokerURL: "ws://채팅서버:61613/stomp/chat",
	...
})
```

### 수정한 전체코드(react)

```jsx
import { useEffect, useRef, useState } from "react"
import { useParams } from "react-router-dom"
import SockJS from "sockjs-client"
import * as StompJs from "@stomp/stompjs"

import { createChatBubble } from "../utils/createChatBubble" // 채팅 말풍선 생성
import { getUserToken } from "../utils/getUserToken" // 유저 토큰 가져오기

let stompClient
let subscription

function ChatRoom() {
  // stomp & user
  const { roomId } = useParams()
  const textInputRef = useRef()
  const messageListRef = useRef()
  const userId = "유저아이디"
  const [token, setToken] = useState("유저토큰")

  // 채팅방 구독
  function subscribe() {
    if (stompClient != null) {
      subscription = stompClient.subscribe(
        `/exchange/chat.exchange/room.${roomId}`,
        (content) => {
          const payload = JSON.parse(content.body)
          console.log(payload)

          const bubble = createChatBubble({ payload, userId })
          messageListRef.current.appendChild(bubble)

          messageListRef.current.scrollTop = messageListRef.current.scrollHeight
        },
      )
    }
  }

  // stompClient 생성
  useEffect(() => {
    if (token !== "") {
      stompClient = new StompJs.Client({
        brokerURL: "ws://채팅서버:61613/stomp/chat",
        connectHeaders: {
          login: "user",
          passcode: "password",
          Authorization: token,
        },
        debug: function (str) {
          console.log(str)
        },
        onConnect: () => {
          // 연결 됐을 때 구독 시작
          subscribe()
        },
        onStompError: function (frame) {
          console.log("Broker reported error: " + frame.headers["message"])
          console.log("Additional details: " + frame.body)
        },
        onDisconnect: () => {
          disConnect()
        },
        reconnectDelay: 5000,
        heartbeatIncoming: 4000,
        heartbeatOutgoing: 4000,
      })
      stompClient.webSocketFactory = function () {
        return new SockJS("http://채팅서버:8080/stomp/chat")
      }

      stompClient.activate()
      textInputRef.current.focus()
    }

    // 컴포넌트 언마운트 될 때 웹소켓 연결을 끊기
    return () => {
      disConnect()
    }
  }, [token])

  // 채팅 연결 끊어질 때
  async function disConnect() {
    if (stompClient && subscription) {
      stompClient.deactivate()
      subscription.unsubscribe()
      console.log("채팅 연결 끊어짐")
    }
  }

  // 메세지 전송
  async function handleSubmit(event) {
    event.preventDefault()
    const input = event.target.querySelector("input")
    if (input.value === "") return

    const message = {
      message: input.value,
      userId,
      profileImage: "{유저 프로필 이미지 주소}",
      senderName: "{유저 닉네임}",
    }

    const token = await getUserToken()

    stompClient.publish({
      destination: `/pub/chat.message.${roomId}`,
      body: JSON.stringify(message),
      headers: {
        Authorization: token,
      },
    })
  }

  return (
    <div>
      {/* 채팅 내용 나타나는 부분 */}
      <ul ref={messageListRef}></ul>

      {/* 채팅 전송 */}
      <form onSubmit={handleSubmit}>
        <div>
          <input ref={textInputRef} type="text" />
          <button type="submit">보내기</button>
        </div>
      </form>
    </div>
  )
}

export default ChatRoom
```

## 참고 레퍼런스

[https://dev-gorany.tistory.com/m/325?category=901854#js](https://dev-gorany.tistory.com/m/325?category=901854#js)  
[https://stomp-js.github.io/guide/stompjs/using-stompjs-v5.html](https://stomp-js.github.io/guide/stompjs/using-stompjs-v5.html)