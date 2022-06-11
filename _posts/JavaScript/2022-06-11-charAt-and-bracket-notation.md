---
title: "string.charAt(i), string[i] 차이점"
excerpt: "문자열에서 개별 문자 접근 방법, 약간의 영어 공부를 곁들인"

categories:
 - JavaScript
tags:
 - String
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

그동안 아무생각 없이 문자열을 대괄호에 인덱스 넣어서 가져와 썼었는데 `.charAt()`을 보고 굳이 이걸 쓰는 이유가 궁금해서 찾아봤다. 

## 참고 문서

[https://stackoverflow.com/questions/5943726/string-charatx-or-stringx](https://stackoverflow.com/questions/5943726/string-charatx-or-stringx)
[https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/charAt](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/charAt)
[https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String)

## charAt(인덱스)

문자열에서 인자로 주어진 인덱스에 해당하는 문자를 리턴. 존재하지 않는 인덱스가 주어지면 빈문자열이 나온다. 아래 대괄호 접근법은 그냥 배열에서도 쓸 수 있지만 `charAt()`은 문자열에만 쓸 수 있다. 

## 문자열[인덱스]

문자열을 배열같은 오브젝트로 취급하는 방법. 존재하지 않는 인덱스를 넣으면 `undefined`를 반환한다. 스택오버플로우와 mdn 문서를 보니 이 접근 방법은 오래된 브라우저는 지원하지 않는다. ES5 지원여부 때문인거 같은데, 2022년 현재에는 굳이 신경쓸 부분은 아닌듯. 

## 예시

```javascript
'hello'[NaN] // undefined
'hello'.charAt(NaN) // 'h'
''[-2] // undefined
''.charAt(-2) // ''

'hello'[true] //undefined
'hello'.charAt(true) // 'e'
```

## 덤으로 영어 공부

| 오늘의 단어 | 뜻 |
| --- | --- |
| bracket notation | 대괄호 표기법 |
| brace | 중괄호 |
| implement | 시행하다 |
| character access | 문자 접근 |

### 예문
1. What is the use of charAt() method?
2. How String.charAt() is implemented in JavaScript?

### 'character'의 약어'char' 발음

[여기](https://english.stackexchange.com/questions/60154/how-to-pronounce-the-programmers-abbreviation-char)를 참고하면 뭐가 확실히 답이라고 할 수 없는 듯하다. [챠ㄹ]정도로 발음하면 무난하겠다. 
'charset(character set)'을 [케어셋]이 아닌 [챠ㄹ셋]으로 발음하는 걸 떠올려야겠다. 