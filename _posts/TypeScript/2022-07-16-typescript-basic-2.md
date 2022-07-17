---
title: "TypeScript 클래스"
excerpt: "TypeScript에서 클래스"

categories:
 - TypeScript
tags:
 - '스터디'
# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

## 클래스
### 클래스 소개
`Class` 키워드를 쓰고 이름은 파스칼 케이스로 쓴다.  
필드에 `readonly`를 붙이면 수정이 불가능하다. 
```tsx
class Person {
  /**
   * 필드
   * - 일종의 속성
   * - public으로 사용가능합니다.
   */
  name: string;
  age: number;
  readonly location: string = "Korea";
}

/**
 * 인스턴스
 * - 클래스에서 파생된 고유한 것
 * - 실제로 생성된 후 메모리에 올라감
 */
const p = new Person()
```

- JavaScript의 클래스는 ES2015의 새로운 문법. 
- TypeScript에서의 클래스 기능은 C#에서 유래된 것이 많다. 
- 일부 기능은 TS에서만 존재하는 고유 문법으로 컴파일 후에 사라진다.
- 자바스크립트와 타입스트크립트의 클래스는 다르다. 

### 생성자, 메서드
생성자: 초기화를 담당  
메서드: 객체(클래스)에서는 행동을 뜻한다. 함수랑 비슷.
```tsx
class Person {
  name: string;
  age: number;
  readonly location: string = "Korea";

  // 생성자
  constructor(name: string, age: number) {
      this.name = name
      this.age = age
  }

  // 메서드
  introduce(): string {
    return `${this.name}의 나이는 ${this.age} 입니다.`
  }
}
```

### getter setter
필드에 접근할 권한을 가진 제어자
 
getter O / setter X => 속성은 자동으로 읽기 전용  
setter 매개변수의 타입 X / getter의 반환 타입에서 추론
 
private 속성은 `.`연산자로 접근할 수 없다.
```tsx
class Person {
  name: string;
  private _age: number;

  constructor(name: string, age: number) {
      this.name = name
      this._age = age
  }

  get age() {
    if (this._age === 0) {
      return '설정되지 않았습니다.'
    }

    return `나이는 ${this._age}세로 추정됩니다.`
  }

  set age(age) {
    if (typeof age === 'number') {
      this._age = age
    }

    this._age = 0;
  }
}

const p = new Person('Jang', 0)
console.log(p.age) // "설정되지 않았습니다."
console.log(p.name) // "Jang"
```

### extends
extends: 상속, 확장
```tsx
class 기본 {
  result() {
    return 'Base'
  }
}

class 파생 extends 기본 {
  result() {
    return 'Derived'
  }
}

const de = new 파생()

console.log(de.result()) // "Derived"
```

### super
기본 클래스 호출시 사용  
*생성자에서 this 사용 전 호출되어야함*
```tsx
class Animal {
  name: string

  constructor(name: string) {
    this.name = name
  }

  sayName() {
    return `동물의 이름은 ${this.name}`
  }
}

class Person extends Animal {
  constructor(name: string) {
    super(name)
  }

  sayName() {
    return `${super.sayName()} 사람의 이름은 ${this.name}`
  }
}

class Jang extends Person {
  
}

const person = new Person('Jang')

console.log(person.sayName()) // "동물의 이름은 Jang 사람의 이름은 Jang"
```

### 접근 제어자

```tsx
class Person {
  public name: string
  private age: number
  protected gender: 'M' | 'F'

  constructor(name: string, age: number, gender: 'M' | 'F') {
    this.name = name
    this.age = age
    this.gender = gender
  }

  sayName() {
    return `이름은 ${this.name} 입니다`
  }

  protected sayAge() {
    return `나이는 ${this.age}`
  }

  private sayGender() {
    return `성별 타입은 ${this.gender}`
  }
}

class Me extends Person {
  constructor(name: string, age: number, gender: 'M' | 'F') {
    super(name, age, gender)
  }

  sayInfo() {
    return `${super.sayGender()} ${super.sayAge()} ${super.sayName()}`
  }
}

const p = new Person('jang', 99, 'M')

// Errors
// Property 'sayGender' is private and only accessible within class 'Person'.
```

- 속성과 메서드에 접근을 제한할 수 있다.
- 클래스 내부 구현 정보를 적당히 공개하여 일부분만 노출시킨다
    - API와 비슷한 흉내를 낼 수 있다.
    - 타입 시스텝을 이용해 규칙을 강제할 수 있다.

| 제어자 | 설명 |
| --- | --- |
| public | 어디서나 접근 가능 (기본값) |
| protected | 해당 클래스와 서브클래스에서만 접근 가능 |
| private | 해당 클래스에서만 접근 가능 |

[Private class fields - JavaScript \| MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes/Private_class_fields)  
자바스크립드 고유의 프라이빗 기능이 따로 생겼다. ES2019에서 해쉬 `#` 를 사용한다.

### static
```tsx
class StaticClass {
  private static type = 'Type'
  // static name = 'name' 
  // Static property 'name' conflicts with built-in property 'Function.name' of constructor function 'StaticClass'.

  static getType() {
		// return this.type
    return StaticClass.type
  }
}

console.log(StaticClass.type) // Property 'type' is private and only accessible within class 'StaticClass'.
console.log(StaticClass.getType()) // "Type"
```

- 클래스의 속성과 메서드를 new로 인스턴스화 하지 않고 호출할 수 있다.
- 접근 제어자를 활용할 수 있다.
- 몇가지 정적 이름을 사용할 수 없다.
    - 클래스는 그 자체로 new로 호출할 수 있는 함수이기 때문
    - 예) [function.name](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/name)

constructor는 new로 인스턴스를 만들어서 초기화 할 때 쓰이기 때문에 여기서는 사용하지 않았음.

### readonly
```tsx
class Person {
  name: string
  readonly age: number
  
  constructor(name: string, age:number) {
    this.name = name
    this.age = age
  }

  setAge(newAge: number) {
    this.age = newAge // Cannot assign to 'age' because it is a read-only property.
  }
}

const p = new Person('Jang', 99)

p.name = 'JJJ'
p.age = 100 // Cannot assign to 'age' because it is a read-only property.
console.log(p)
```

### 추상 클래스

```tsx
abstract class Animal {
  // 선언된 메서드 => 파생 클래스에서 구현해야 한다.
  abstract hello(): string
 
  // 구현된 메서드
  run() {
    return this.hello() + ' run'
  }
}
 
// 직접 인스턴스가 될 수 없다.
// const animal = new Animal()

class Person extends Animal {
  hello() {
    return 'Person'
  }
}

class Dog extends Animal {
  hello() {
    return 'Dog'
  }
}

const person = new Person()
const dog = new Dog()

console.log(person.hello()) // "Person"
console.log(dog.hello()) // "Dog"
```

- `abstract`를 선언한 클래스로 직접 인스턴스화 될 수 없는 클래스이다.
- 직접 인스턴스화 될 수 없지만 `extends` 후 파생된 클래스를 인스턴스화하도록 유도한다.
- 추상 클래스는 구현된 메서드를 포함시킬 수 있다.
- `abstract` 선언한 메서드는 파생된 클래스에서 메서드를 구현해야한다. (강제성)
- 설계의 목적을 명확히 가지는 클래스.

### Parameter Properties
```tsx
class Person {
  constructor(
    public name: string,
    private age: number,
    protected gender: 'M' | 'F'
    ) {
  }
  sayName() {
    return `이름은 ${this.name} 입니다`
  }

  protected sayAge() {
    return `나이는 ${this.age}`
  }
}

class Me extends Person {
  constructor(name: string, age: number, gender: 'M' | 'F') {
    super(name, age, gender)
  }

  sayInfo() {
    return `${super.sayAge()} ${super.sayName()}`
  }
}

const p = new Person('jang', 99, 'M')
```

- 타입스크립트에서만 존재
- 가독성은 조금 떨어지지만 반복을 줄여주고 편하다.

**반복해서 썼던 부분을**
```tsx
class Person {
  public name: string
  private age: number
  protected geneder: 'M' | 'F'

  constructor(name: string, age: number, gender: 'M' | 'F') {
    this.name = name
    this.age = age
    this.gender = gender
  }

}
```
    
**이렇게 한 번에 쓸 수 있다**
```tsx
class Person {
  constructor(
    public name: string,
    private age: number,
    protected gender: 'M' | 'F'
    ) {
  }

}
```

### 메서드 오버라이딩
확장한 클래스에서 메서드를 오버라이딩 할 수 있다. 기본 클래스의 메서드는 그대로 유지된다.
```tsx
class Animal {
  run() {
    return 'Animal이 달리다'
  }
}

class Dog extends Animal {
  run() {
    return 'Dog가 달리다'
  } 
}
 
class Person extends Animal {
  run() {
    return 'Person이 달리다'
  } 
}

const a = new Animal()
const p = new Person()
const d = new Dog()

console.log(a.run()) // "Animal이 달리다"
console.log(p.run()) // "Person이 달리다"
console.log(d.run()) // "Dog가 달리다"
```

