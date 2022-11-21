---
title: "TeamRocket 프로젝트"
excerpt: "팀프로젝트 정리 문서"

categories:
  - etc
tags:
  - 팀프로젝트
  - TeamRocket
# last_modified_at: 2022-07-10
toc: true

published: false
---

## 로켓단의 한국정복

주제: 여행 & 일상 공유 Social Media

진행 기간: 2022.10.10 ~ 2022.11.18 (6주)

팀 구성원: 프론트엔드 2명 + 백엔드 4명

깃허브: [https://github.com/TeamRocketDan](https://github.com/TeamRocketDan)

## 개발 스택

프론트엔드 개발 스택

- HTML, CSS
- JavaScript
- React
- Recoil, Recoil-persist
- React-router-dom
- stompjs
- FontAwesome
- Tailwindcss
- kakao map API

<a href="/etc/team-rocket-skills" target="_blank"><i class="fas fa-link"></i> 개발스택 선정 이유</a>

## 개발 타임라인

### 1주차

- 프로젝트 기획 및 화면 구성 설계
- 프론트엔드 기술 스택 선정
- 웹소켓 공부(zoom 클론 코딩하면서 배우는 웹소켓)

### 2주차

- 채팅 구현 자료 조사(stompjs)
- 프론트엔드 프로젝트 초기 설정
- 채팅방 리스트, 채팅방 레이아웃 개발 진행

### 3주차

- 헤더에서 지역 선택 기능 개발
- 로그인 개발 (useSearchParams로 토큰 가져와서 저장하기, recoil persist로 로컬스토리지 이용하기)
- 만료된 토큰 재발급 기능 추가(jwt-decode 이용)
- 채팅방 생성 페이지 개발 (폼 데이터 post 요청)
- 헤더 메뉴에 로그아웃 기능 추가
- 채팅 리스트를 카카오지도API 이용해서 마커 표시하기

### 4주차

- 채팅 리스트 페이지 개발 (쿼리 스트링으로 페이지네이션 저장, 지역 채팅 불러오기 기능 수정)
- 내 채팅 리스트 받아오기
- 토큰 재발급 요청을 만료 5분 전으로 수정
- 채팅방에서 요청 보낼 때 유저 토큰도 보내기

### 5주차

- 채팅 서버와 채팅방 테스트
- 프론트엔드 배포 테스트 (Netlify를 이용한 배포, http로 요청 보내기)
- 채팅방에서 이전 메세지 불러오기 기능 개발
- 채팅방 나가기 기능 추가
- 채팅방 입장, 퇴장 메세지 보내기
- 채팅 시간 표시
- 채팅방 정보 불러와서 제목, 참가자 표시하기
- 채팅방에서는 공통 헤더 안보이게 (Outlet이 생각대로 안될 때)
- 로그인 안했으면 마이페이지 접근했을 때 로그인 페이지로 이동(커스텀 hook)

### 6주차

- 채팅방 메세지 데이터 수정
- 채팅 리스트 지역 선택 안했을 경우 전체 데이터가 보이도록 수정
- 채팅 리스트 반응형으로 페이지 사이즈 변경해서 다시 렌더링
- 페이지네이션 개선
- 마지막까지 계속되는 QA 수정
- 문서 정리 및 발표 자료 작성

## trouble shooting
<a href="/javascript/stompjs5-react/" target="_blank"><i class="fas fa-link"></i> stompjs 2 → 5버전에 맞춰 리액트 코드 작성하기</a>


## 프로젝트 마무리하며



후기 요약 ⇒ 타입스크립의 필요성을 느꼈다.