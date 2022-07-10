---
title: "JavaScript 기본 정리 1"
excerpt: "JacaScript 기본 문법, 개발 환경 등"

categories:
 - JavaScript
tags:
 - '스터디'
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

## 기본 문법

### **Lexical Grammar(어휘 문법) mdn**

[어휘 문법 - JavaScript MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Lexical_grammar)

주요한 문법

- 개행 문자: JS코드 실행에 영향을 미친다.
- 주석
- 키워드, 예약 키워드: 사용하면 어떤 action이 일어남
- 리터럴
- 자동 세미콜론 삽입

## 개발환경 및 도구

- 생태계: 영어로 구글 검색. 
브라우저는 크롬, 파이어폭스, 사파리, 웨일 추천  
MDN 문서를 최대한 많이 보자. 최신 ECMAScript가 기술되어 있다.  
모던 자바스크립트 튜토리얼 [https://ko.javascript.info/](https://ko.javascript.info/)  
[모던 자바스크립트 Deep Dive 책](https://book.naver.com/bookdb/book_detail.naver?bid=16710547)  
PoiemaWeb [https://poiemaweb.com/](https://poiemaweb.com/)
- 에디터: VSCode  
확장: Live Server
- github: 코드 저장 클라우드로 활용하면서 입문 + 공부 내용 정리
- Node.js (노드의 버전을 관리해주는 NVM을 먼저 깔기를 추천)
- 터미널 도구: VSCode의 내장 터미널도 OK,  
window는 PowerShell.  
그 외 Hyper.js, iTerm + zsh 추천
- codesandbox: 브라우저에서 테스트, 깃헙 연동 가능

## 웹 브라우저

- 마크업 언어: HTML, XML, SVG
- DOM mdn 문서를 보자 
[https://developer.mozilla.org/ko/docs/Web/API/Document_Object_Model/Introduction](https://developer.mozilla.org/ko/docs/Web/API/Document_Object_Model/Introduction)
- console.dir(document.querySelector(’body’)) 를 살펴보자

## 문법

- 값 value: 하나의 값
- 식 또는 표현식 expression: 값을 생성, 값으로 귀결
- 문 statement: 행동을 한다. 흐름을 제어한다. 지시문으로 인터프리터에게 명령한다.

**'use strict'**  
엄격 모드. (this, 변수 등에 대해서…)  
ES2015 이후 모듈에서는 기본적으로 엄격하기 때문에 따로 신경쓸 필요 없다. 
예전 JS 코드를 다뤄야 할 때 사용하면 좋다. 