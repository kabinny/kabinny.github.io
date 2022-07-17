---
title: "TypeScript 인터페이스"
excerpt: "interface implements, extends"

categories:
 - TypeScript
tags:
 - '스터디'
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

## 인터페이스
### interface 소개
interface: 정의, 설계  
implements: 구현
```tsx
interface Person {
  name: string
  age: number
}

const jang: Person = {
  name: 'Jang',
  age: 99
}
```
**js 컴파일 결과**
```javascript
const jang = {
  name: 'Jang',
  age: 99
};
```

- JavaScript에서는 존재하지 않는다. (다른 객체 지향 언어에 존재한다.)
- 자바스크립트로 컴파일 되지 않는다.
- 객체의 타입을 정의하고 생김새를 가지도록 할 수 있다.
- TypeScript에서의 클래스 기능은 C#에서 유래된 것이 많다.
- 일부 기능은 TS에서만 존재하는 고유 문법으로  컴파일 후에 사라진다.
- `extends`가 아닌 `implements` 키워드로 구현한다.

### interface implements
implements는 인터페이스의 설계를 구현한다.   
아래 예시의 Jang 클래스는 AnyClass를 확장하고, Animal과 Person 클래스를 implement했다.  
=> 다중으로 implement 할 수 있다.
```tsx
interface Animal {
  name: string
  run(): string
}

interface Person {
  sayName(): string
}

class AnyClass {
}

class Jang extends AnyClass implements Animal, Person {
  constructor(public name: string) {
    super()
  }

  run() {
    return this.name
  }

  sayName() {
    return `사람의 이름은 ${this.name}`
  }
}
```



### interface extends
extends를 사용하면 합쳐진다-라고 생각할 수 있다. 
하지만 충돌해서는 안된다!
```tsx
interface Animal {
  name: string
  run(): string
}

interface Person extends Animal {
  sayName(): string
}

const jang: Person = {
  name: 'Jang',
  run() {
    return 'string'
  },
  sayName() {
    return 'string2'
  }
}
```

