---
title: "JSDoc @param 태그 사용하기"
excerpt: "@param 사용 예시"

categories:
 - JavaScript
tags:
 - JSDoc
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

JSDoc은 주석을 형식에 맞게 작성하면 보기 편하게 문서로 만들어 주기 때문에 본격적으로 문서화할 때 사용해보고 싶었다. 매력적인 JSDoc을 잘 써보고 싶기 때문에 가장 많이 사용하게 되는 태그 중 하나인 @param 사용법을 정리해본다. 

## 참고

[https://jsdoc.app/tags-param.html](https://jsdoc.app/tags-param.html)

생략과 오역이 있을 수 있다. 

## 개요

@param 태그는 함수 파라미터의 이름, 타입, 설명을 제공한다.  

@param 태그는 파라미터의 이름이 필요하다. 파라미터의 타입(중괄호 안에)이나 설명을 추가할 수 있다. 

파라미터 타입은 자바스크립트 기본 타입이거나(string, Object…), JSDoc 변수일 수 있다. JSDoc이 자동으로 그 이름에 해당하는 부분에 연결한다. 또한 타입 표현식을 사용할 수 있는데(nullable 하거나 어떤 타입도 가능하다거나) @type 태그 설명서를 참고.  

설명을 추가할 때 가독성을 위해 하이픈(-)을 설명 앞에 추가할 수 있다. 하이픈 앞뒤로 공백이 필요하다. 

## 예시

### Names, types, and descriptions

이름, 타입, 설명을 포함하는 방법

**이름만 있을 때**

```jsx
/**
 * @param somebody
 */
function sayHello(somebody) {
    alert('Hello ' + somebody);
}
```

**이름과 타입**

```jsx
/**
 * @param {string} somebody
 */
function sayHello(somebody) {
    alert('Hello ' + somebody);
}
```

**이름, 타입, 설명**

```jsx
/**
 * @param {string} somebody Somebody's name.
 */
function sayHello(somebody) {
    alert('Hello ' + somebody);
}
```

**이름, 타입, 하이픈 추가한 설명**

```jsx
/**
 * @param {string} somebody - Somebody's name.
 */
function sayHello(somebody) {
    alert('Hello ' + somebody);
}
```

### **Parameters with properties**

속성이 있는 파라미터. 파라미터에 특정 속성이 있는 경우 @param 태그를 추가로 써서 문서화할 수 있다.

**파라미터의 속성 문서화하기**

```jsx
/**
 * 프로젝트를 직원에게 할당합니다. 
 * @param {Object} employee - 프로젝트에 배정된 직원
 * @param {string} employee.name - 직원의 이름
 * @param {string} employee.department - 직원의 부서
 */
Project.prototype.assign = function(employee) {
    // ...
};
```

**구조 분해 할당된 파라미터**

이름없이 구조 분해 할당해서 사용할 때는 적절한 이름을 붙이고 문서화에 사용하면 된다. 

```jsx
/**
 * 프로젝트를 직원에게 할당합니다. 
 * @param {Object} employee - 프로젝트에 배정된 직원
 * @param {string} employee.name - 직원의 이름
 * @param {string} employee.department - 직원의 부서
 */
Project.prototype.assign = function({ name, department }) {
    // ...
};
```

**파라미터가 배열**

이 JSDoc 문법은 배열 파라미터에도 사용할 수 있다. 

```jsx
/**
 * 프로젝트를 직원 리스트에 할당합니다. 
 * @param {Object[]} employees - 프로젝트에 배정된 직원들
 * @param {string} employees[].name - 직원의 이름
 * @param {string} employees[].department - 직원의 부서
 */
Project.prototype.assign = function(employees) {
    // ...
};
```

### **Optional parameters and default values**

파라미터가 선택적일 때, 기본값을 넣을 때의 예시

**선택적 파라미터 (JSDoc syntax)**

```jsx
/**
 * @param {string} [somebody] - Somebody's name.
 */
function sayHello(somebody) {
    if (!somebody) {
        somebody = 'John Doe';
    }
    alert('Hello ' + somebody);
}
```

**선택적 파라미터(Google Closure Compiler syntax)**

```jsx
/**
 * @param {string=} somebody - Somebody's name.
 */
function sayHello(somebody) {
    if (!somebody) {
        somebody = 'John Doe';
    }
    alert('Hello ' + somebody);
}
```

**기본값이 있는 선택적 파라미터**

```jsx
/**
 * @param {string} [somebody=John Doe] - Somebody's name.
 */
function sayHello(somebody) {
    if (!somebody) {
        somebody = 'John Doe';
    }
    alert('Hello ' + somebody);
}
```

### **Multiple types and repeatable parameters**

파라미터가 여러 타입이거나 any 타입일 때, 반복 가능한 파라미터 예시

타입 관련은 [@type 문서](https://jsdoc.app/tags-type.html)를 살펴보세요. 

**타입이 하나 이상 (type union)**

```jsx
/**
 * @param {(string|string[])} [somebody=John Doe] - Somebody's name, or an array of names.
 */
function sayHello(somebody) {
    if (!somebody) {
        somebody = 'John Doe';
    } else if (Array.isArray(somebody)) {
        somebody = somebody.join(', ');
    }
    alert('Hello ' + somebody);
}
```

**any 타입**

```jsx
/**
 * @param {*} somebody - Whatever you want.
 */
function sayHello(somebody) {
    console.log('Hello ' + JSON.stringify(somebody));
}
```

**반복 가능한 파라미터**

(파라미터로 숫자 여러개가 전달된 경우)

```jsx
/**
 * 함수에 전달된 모든 숫자들의 합을 반환
 * @param {...number} num - 양수거나 음수
 */
function sum(num) {
    var i = 0, n = arguments.length, t = 0;
    for (; i < n; i++) {
        t += arguments[i];
    }
    return t;
}
```

### **Callback functions**

파라미터로 콜백 함수를 받는다면, 콜백 타입을 정의하기 위해 @callback 태그를 사용한다. 그리고 나서 콜백 타입을 @param 태그에 넣는다. 

**콜백 함수 파라미터**

```jsx
/**
 * This callback type is called `requestCallback` and is displayed as a global symbol.
 *
 * @callback requestCallback
 * @param {number} responseCode
 * @param {string} responseMessage
 */

/**
 * 비동기적으로 무언가를 하고, 콜백을 실행
 * @param {requestCallback} cb - 응답을 처리하는 콜백
 */
function doSomethingAsynchronously(cb) {
    // code
};
```

## 마무리

필요한 것만 빨리 나오길 바라면서 훑어봤을 때보다 더 이해가 잘 된다. 짧게 여러본 본 것보다 꼼꼼히 한 번 본 게 더 도움이 되었다. 실제 실행 코드에서는 사용되지 않더라고 임의로 이름을 지어야 하는 부분이 생각보다 많고, 그게 더 문서화 됐을 때 이해하기 좋은 거 같긴 하다.