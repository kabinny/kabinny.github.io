---
title: "TypeScript 기본 정리 1"
excerpt: "TypeScript란, TypeScript의 기본 타입"

categories:
 - TypeScript
tags:
 - '스터디'
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

## 0. 시작하기

### TypeScript란?

타입 구문이 있는 자바스크립트

- VSCode와의 호환성이 좋아서 오류 캐치가 가능
- 최근 트렌드에 따르면 인기가 상승중
- JavaScript로 컴파일 되어서 JavaScript가 지원되는 모든 곳에서 실행 가능
- 비공식적 가이드라인이 많고 스타일이 다양하다.
- [타입스크립트 플레이그라운드](https://www.typescriptlang.org/ko/play)에서 간단히 실행해 볼 수 있다.

## 1. 기본 타입 훑어보기

### type annotation

**annotate: 주석을 달다*

변수명 우측에 콜론붙이고 타입을 명시하는 것

```tsx
/**
 * Type Annotation
 * 
 * value: type
 */

const val: number = 123 // val 이라는 변수는 숫자 타입이다
```

### 원시 타입

문자열, 숫자, 불리언, 빅인트 등…

```tsx
/**
 * Primitive (원시값)
 * 
 * - 불변
 * - 객체가 아닌 값들
 */
// 각각 문자열, 숫자, 불리언, 빅인트라고 타입을 명시했다
const str: string = 'STRING'
const num: number = 123
const bool: boolean = true
const big: bigint = 100n

// 이렇게 기본값을 지정할 수 있다.
// 정해진 값 외의 값이 들어오면 에러
const str: 'string' = 'string'
const str: string = 'STRING' // error
const num: 123 = 123
const num: 123 = '123' // error
const bool: true = true
const bool: true = false // error

// 타입을 명시하지 않았는데 자동으로 타입추론이 된다. 
const str = 'STRING' // string
const num = 123 // number
const bool = true // boolean
```

### 객체 타입

이 외에도 객체에 타입을 다는 방법은 다양하다. 

```tsx
/**
 * Object Type
 */
// any와 다른게 없는 상황
// 타입에러는 없지만, 객체는 내부에 많은 것이 있기 때문에 자세히 묘사하기 어렵다. 
const obj: object = {
  str: "str",
  num: 123,
  child: {
    str: "str",
    num: 123,
  },
};

// obj.num 
// 에러!
// Property 'num' does not exist on type 'object'.

// ---

const obj2: {
  str: string;
  num: number;
  child: {
    str: string;
    num: number;
  };
} = {
  str: "str",
  num: 123,
  child: {
    str: "str",
    num: 123,
  },
};

console.log(obj2.str);
```

### 함수 타입

```tsx
/**
 * Function Type
 * 
 * 1. 매개변수
 * 2. 반환
 */
function func(num: number, str: string): string { // 반환 값이 문자열이라는 것
    return num + str
}

func(123, 'str')

function func2(num1: number, num2: string) {
     return num1 + Number(num2)
}

func2(123, '123')

function func3(num1: number, num2: string): void { // void: 반환하지 않는다
    console.log(num1 + num2)
}

func3(123, '123')

const func4 = (str1: string, str2: string) => {
    return str1 + ' ' + str2
}

func4('hello', 'world')

const func5 = (obj: { str1: string, str2: string }) => {
    return obj.str1 + ' ' + obj.str2
}

func5({ str1: 'hello', str2: 'world' })
```

매개변수와 반환 타입을 명시할 수 있다. 

반환 타입은 타입 추론이 잘 돼서 꼭 넣을 필요는 없다. 

### 배열 타입

```tsx
/**
 * Array Type
 */
// 하나의 타입으로만 지정된 배열
const strArr: string[] = ['str', 'str2', 'str3']

const strArr2: Array<string> = ['str', 'str2', 'str3']

const numArr: Array<number> = [1, 2, 3]

const boolArr: boolean[] = [false, true, false, true]

strArr.push(1) // Argument of type 'number' is not assignable to parameter of type 'string'.

numArr.push('str') // Argument of type 'string' is not assignable to parameter of type 'number'.

boolArr.push(false)

// Tuple
// 여러 타입이 있는 배열은 튜플 타입을 이용해야 한다. 
const arr = ['str', 123, false]
```

### 리터럴 타입

let으로 선언된 변수는 재할당으로 값이 바뀔 수 있다. ⇒ 이 변수의 값이 변경될 가능성이 있다는 것을 알린다. ⇒ 잠재적으로 무한한 경우의 값이 들어올 수 있다. 

반면 const로 선언된 변수는 변경되지 않는다고 타입스크립트에 알린다. ⇒ 변경되지 않기 때문에 타입을 유한한 개수로 좁힐 수 있다. 

```tsx
/**
 * Literal Type
 */
let letString = 'Hello'

letString = letString + ' World'

console.log(letString)

const constString = 'Hello'

constString

// ---

let letNumber = 123

letNumber = letNumber + 123

console.log(letNumber)

const constNumber = 123

constNumber
```

#### .D.TS

d.ts는 타입스크립트의 타입 추론을 돕는 파일이다. typescript playground에서는 오른쪽 부분에서 바로 확인 가능하다. 

```jsx
/**
 * Literal Type
 */
declare let letString: string;
declare const constString = "Hello";
declare let letNumber: number;
declare const constNumber = 123;
```

`constString` 은 고정되므로 타입을 “Hello”로 정한다. 

`letString` 은 변경될 수 있으므로 타입을 문자열로 선언한다. 

### 튜플 타입

```tsx
/**
 * Tuple Type
 * 
 * - 길이 고정 & 인덱스 타입이 고정
 * - 여러 다른 타입으로 이루어진 배열을 안전하게 관리
 * - 배열 타입의 길이 조절
 */
const arr: string[] = ['A', 'B', 'C']

const tuple: [number, string] = [1, 'A'] // 0인덱스에는 숫자, 1인덱스에는 문자열

const tuple0: (string | number)[] = [1, 'A'] // Union Type: 변수의 값이 여러 타입을 가질 때

const tuple2: [number, ...string[]] = [0, 'A', 'B'] // 1인덱스 부터는 문자열(가변적 길이)
```

### undefined & null

```tsx
/**
 * undefined & null
 * 
 * JavaScript에서와 마찬가지로
 * 고유의 특별한 타입으로 인정한다.
 * 
 * 이외에 void, never와 같이 더 세밀한 타입도 제공
 * 
 * strictNullChecks가 핵심
 */
// 둘 중 하나만 사용하는 일관성을 가지는 것을 권장. 
const nu: null = null;
const un: undefined = undefined;

console.log(nu == un) // true
console.log(nu === un) // false

// declare function sayHello(word: string): string | null;
// 리턴 타입을 지정하지 않았지만 string 혹은 null이라고 추론됨
function sayHello(word: string) {
    if (word === 'world') {
        return 'hello' + word
    }

    return null
} 

// eclare function log(message: string | number): void;
// 반환하는 게 없기 때문에 알아서 void로 지정됨
function log(message: string | number) {
    console.log(message)
}
```

### any

```tsx
/**
 * any
 * 
 * 모든 값(타입)의 집합
 * 사용하지 말자
 * 
 * noImplicitAny or strict 옵션 true 권장
 */

// declare function func(anyParam: any): void;
// 파라미터의 타입 지정을 안했다. 타입스크립트 컴파일러는 any라고 생각한다..
// 명시적으로 :any를 넣는 것도 지양해야 한다. 
function func(anyParam) {
    anyParam.trim() // 문자열에만 쓸 수 있는 .trim()인데 배열이 들어와도 에러가 나지 않는다.
}

func([1,2,3])
```

any가 붙으면 타입스크립트가 아닌 자바스크립트처럼 사용 가능하게 된다. 

noImplicitAny 옵션이 true 인 경우 “Parameter 'anyParam' implicitly has an 'any' type.” 이라는 에러가 나온다.  

**implicit: 암시된, 내포되는* 

### unknown

```tsx
/**
 * unknown
 * 
 * 새로운 최상위 타입
 * any처럼 모든 값을 허용하지만 상대적으로 엄격하다.
 * TS에서 unknown으로 추론하는 경우는 없으니 개발자가 직접 명시해야함
 * assertion 혹은 타입 가드와 함께 사용한다.
 */
let num: unknown = 99;

// 타입 가드: 타입이 맞는 지 검즘 
if (typeof num === 'string') {
    num.trim();
}

// assertion
(num as string).trim();

// x: any일 경우 에러가 나지 않는다. 
// x: unknown일 경우 val1, val2 제외하고 에러가 발생한다. 
function func(x: unknown) {
	let val1: any = x; 
	let val2: unknown = x;
	let val3: string = x; // Type 'unknown' is not assignable to type 'string'.
	let val4: boolean = x; // Type 'unknown' is not assignable to type 'boolean'.
	let val5: number = x; // Type 'unknown' is not assignable to type 'number'.
	let val6: string[] = x; // Type 'unknown' is not assignable to type 'string[]'.
	let val7: {} = x; // Type 'unknown' is not assignable to type '{}'.
}
```

any에 비해서 비교적 최근 문법. 

**assertion: (사실임을) 주장 = claim*

### void

언제 보이드 타입을 명시하는가? 그냥 타입 추론에 맡기자.
```tsx
/**
 * void
 * 
 * - 함수의 반환이 없는 경우를 명시
 * - 타입 추론에 위임하자
 * 
 * JavaScript에서는 암시적으로 undefined 반환
 * 그러나 void와 undefined는 TypeScript에서 같은 것이 아님
 */

function test(): undefined {
    return undefined
}
console.log(test()) // undefined

function test2(): void {

}

function voidFunc() {
    // declare function voidFunc(): void;
		// void로 알아서 타입 추론이 된다. 
}
```