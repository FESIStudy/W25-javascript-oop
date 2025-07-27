## 객체 지향 프로그래밍이란?

### 주요 개념

- **객체**: 데이터(속성)와 이를 조작하는 함수(메서드)의 집합
- **클래스**: 객체를 생성하기 위한 청사진
- **상속**: 하나의 클래스가 다른 클래스의 속성과 메서드를 재사용
- **다형성**: 동일한 메서드가 다양한 객체에서 다르게 동작
- **캡슐화**: 객체의 내부 상태를 숨기고, 메서드를 통해 접근

<br/>

### 자바스크립트에서의 객체 지향 프로그래밍

OOP는 코드를 구조화하고 재사용성을 높이는 방법이며, 자바스크립트는 **클래스**와 **프로토타입**을 통해 OOP를 지원한다.

<br/>

## 프로토타입

### 프로토타입이란?

- 자바스크립트의 객체 지향 프로그래밍을 구현하는 방식 중 하나
- **모든 객체는 자신의 부모 역할을 하는 프로토타입 객체를 가짐**
- 프로토타입을 통해 객체는 **상속**을 받아 다른 객체의 속성과 메서드 사용 가능
 
<br/>

### 프로토타입 상속과 확장

```tsx
function Dog(name) {
    Animal.call(this, name); // Animal 생성자 함수 호출
}

Dog.prototype = Object.create(Animal.prototype); // Animal 프로토타입을 상속받음
Dog.prototype.constructor = Dog; // Dog.prototype.constructor를 Dog로 설정

Dog.prototype.speak = function() {
    console.log(this.name + ' barks.'); // 메서드 재정의(오버라이딩)
};

let dog = new Dog('Rex');
dog.speak(); // Rex barks.
```

- `someFunction.call(thisArg, arg1, arg2, ...)`: `someFunction`을 즉시 실행하면서 `thisArg`를 `this`로 바인딩한 뒤, 나머지 인자들을 순서대로 넘겨서 실행하는 함수
- `Object.create(proto)`: 새로운 객체를 만들되, 그 객체의 프로토타입(`[[Prototype]])`을 `proto`로 설정하는 함수
    
    ```tsx
    // Dog.prototype을 새 객체로 만드는데, 이 객체의 [[Prototype]]이 Animal.prototype을 참조하게 된다.
    Dog.prototype = Object.create(Animal.prototype);
    ```
    
    ```tsx
    // 프로토타입 체인
    dog → Dog.prototype → Animal.prototype → Object.prototype
    ```
    
 
<br/>

## 클래스 vs 프로토타입

|  | **클래스** | **프로토타입** |
| --- | --- | --- |
| **정의** | 객체를 생성하기 위한 청사진 | 다른 객체를 템플릿으로 사용하여 객체 생성 |
| **도입 버전** | ES6 (ECMA Script 2015) | 자바스크립트 초기 버전 |
| **상속** | 명시적 상속 구조
부모 클래스로부터 자식 클래스가 속성과 메서드를 상속받음 | 객체 간의 직접적인 링크를 통해 상속 |
| **구문** | `class` 키워드를 사용하여 정의 | `Object.create`를 사용하여 구현 |
| **장점** | 명확한 구조, 캡슐화, IDE 지원 | 유연성, 메모리 효율성 |
| **단점** | 유연성 부족, 복잡성 | 혼란 가능성, 지원 부족 |
| **사용 시나리오** | 명확한 구조와 계층이 필요한 대규모 애플리케이션에서 유용 | 자바스크립트를 사용한 동적 웹 애플리케이션에서 주로 사용 |
 
<br/>

### 클래스와 프로토타입의 유연성

자바스크립트의 클래스 문법과 프로토타입 기반 상속은 내부적으로 동작은 유사하지만, **런타임에서 구조(메서드나 속성)를 변경하거나 확장하는 유연성** 측면에서 차이가 있다.

- 클래스 기반
    
    ```tsx
    class Animal {
      speak() {
        console.log('Animal speaks');
      }
    }
    
    const dog = new Animal();
    dog.speak(); // Animal speaks
    
    // 런타임에 메서드 추가 시도
    Animal.prototype.run = function () {
      console.log('Animal runs');
    };
    
    dog.run(); // Animal runs ❗가능은 함
    ```
    
    - 클래스를 사용하더라도, 클래스 또한 `prototype` 기반으로 동작하기 때문에  `prototype`을 직접 수정하면 동작은 한다.
    - 그러나 의도한 사용법이 아니며 코드 유지 보수에 문제가 생기기 쉽다.
- 프로토타입 기반
    
    ```tsx
    function Animal() {}
    
    Animal.prototype.speak = function () {
      console.log('Animal speaks');
    };
    
    const dog = new Animal();
    dog.speak(); // Animal speaks
    
    // 런타임에 메서드 추가
    Animal.prototype.run = function () {
      console.log('Animal runs');
    };
    
    dog.run(); // Animal runs ✅
    ```
    
    - 프로토타입은 객체 구조를 생성하고 나서 어디서든 자유롭게 수정할 수 있다.
    - 메서드 추가/삭제/덮어쓰기가 쉽고, 동적으로 동작을 바꿔야 하는 **프레임워크 개발**에 유리하다.
        - 예) Vue 2의 `Vue.prototype.$emit`처럼 prototype에 기능 주입

| 항목 | 클래스 (`class`) | 프로토타입 (`prototype`) |
| --- | --- | --- |
| 런타임 구조 변경 | 제한적, 엄격 모드, 실무에선 비권장 | 매우 유연, 실무에서도 활용 |
| 메서드 추가 | `Class.prototype.method = fn` 가능하지만 덜 직관적 | 자유롭게 추가 가능 |
| 목적 | 명확한 OOP 구조 설계, 가독성↑ | 동적 변경/확장, 프레임워크/라이브러리 구현 |
| 메서드 정의 | 비열거(enumerable: false), strict mode | 열거 가능, 느슨한 환경 |
| 적합한 분야 | 어플리케이션, 정형화된 구조 | 플러그인 시스템, 유연한 컴포넌트 확장 |

> 클래스는 `의도적으로 제한적인 설계`를 통해 코드 안정성과 유지 보수성을 높이고, 
프로토타입은 `의도적으로 열려 있는 구조`로 동적 확장이 가능한 유연성을 제공한다.
> 
 
<br/>

### 클래스는 프로토타입의 문법적 설탕이다?

- `문법적 설탕`: 기존의 기능을 더 간단하고 읽기 좋게 만든 새로운 문법
- 실제로 클래스는 프로토타입 기반 위에 만들어진다.
    - 클래스도 결국 함수(Function 객체)이고, `new` 키워드로 인스턴스를 만들면 내부적으로 `[[Prototype]]` 연결을 실행한다.
- 그러나 완전히 똑같지는 않다. 클래스에는 제약이 있다.
    
    
    | 항목 | 클래스 (`class`) | 프로토타입 방식 |
    | --- | --- | --- |
    | strict mode | 항상 엄격 모드로 실행됨 | 기본은 비엄격 모드 |
    | constructor 호출 | 반드시 `new`와 함께 사용 | 함수는 그냥도 호출 가능 |
    | 상속 문법 | `extends`, `super` 제공 | 직접 prototype 체인 설정해야 함 |
    | 메서드 정의 | 열거되지 않음 (non-enumerable) | 열거됨 |
    | 동적 생성자 함수 | 불가능 (`class`는 런타임에서 선언 없이 못 씀) | 가능 (`Function`으로 만들 수 있음) |

> 클래스가 프로토타입의 문법적 설탕이라는 말은 맞지만, 완전히 같지는 않다.
> 
 
<br/>

## 믹스인(Mixin)

- 클래스나 객체에 공통 기능을 혼합하여 재사용성을 높이는 패턴
- 상속을 사용하지 않고도 **여러 클래스에 공통 기능을** 추가할 수 있게 해준다.
- 클래스 간 다중 상속이 불가능한 자바스크립트에서 대안으로 자주 사용된다.

```tsx
// 믹스인
let sayHiMixin = {
  sayHi() {
    alert(`Hello ${this.name}`);
  },
  sayBye() {
    alert(`Bye ${this.name}`);
  }
};

// 사용법:
class User {
  constructor(name) {
    this.name = name;
  }
}

// 메서드 복사
**Object.assign(User.prototype, sayHiMixin);**

// 이제 User가 인사를 할 수 있습니다.
new User("Dude").sayHi(); // Hello Dude!
```
 
<br/>

### 믹스인 vs 상속

| 항목 | 믹스인(Mixin) | 상속(Inheritance) |
| --- | --- | --- |
| 재사용 방식 | 필요한 기능만 조합 | 계층적으로 상속 |
| 유연성 | 높음 (다중 믹스인 가능) | 낮음 (단일 상속만 가능) |
| 코드 구조 | 평면적, 조립식 | 계층적 |
| 충돌 위험 | 높음 (같은 메서드가 덮일 수 있음) | 명확함 (상속 우선순위 존재) |
 
<br/>

### 믹스인 사용 예시

- 공통 동작을 여러 클래스나 객체에 반복 없이 제공하고 싶을 때
- 예시) 이벤트 처리, 로깅, 상태 추적, 타이머 기능 등
 
<br/>

### 믹스인 패턴의 단점

- 네임스페이스 충돌 가능성 (여러 믹스인에 동일한 메서드가 있을 경우)
- 깊은 상속보다 관리가 쉬우나, 지나친 사용은 코드 추적이 어려워질 수 있다.
- 타입스크립트에서는 믹스인 타입 정의가 조금 복잡해질 수 있다.
 
<br/>

## 다형성

- 객체 지향 프로그래밍에서 동일한 인터페이스를 통해 서로 다른 데이터 타입을 처리할 수 있게 하는 특성
- 주로 **상속**과 **인터페이스**를 통해 구현된다.
 
<br/>

## 메타프로그래밍

- 프로그램이 자신을 수정하거나, 새로운 행동을 추가하는 기술
    
    → 코드를 동적으로 조작하는 프로그래밍 기법
    
- 자바스크립트에서는 주로 리플렉션(Reflection)과 프록시(Proxy)를 통해 구현된다.
 
<br/>

### 리플렉션

- 객체에 대한 메타 정보(속성, 메서드 등)를 읽거나 조작하는 기법
- 대표적인 리플렉션 도구로 `Reflect` API가 있다.
    
    ```tsx
    const person = { name: 'Alice' };
    
    // 속성 읽기
    Reflect.get(person, 'name'); // 'Alice'
    
    // 속성 설정
    Reflect.set(person, 'name', 'Bob');
    console.log(person.name); // 'Bob'
    
    // 속성 존재 여부 확인
    Reflect.has(person, 'name'); // true
    
    // 속성 삭제
    Reflect.deleteProperty(person, 'name');
    console.log(person.name); // undefined
    ```
    
- `Reflect`를 사용하는 이유
    - 기존의 `obj[prop]`, `delete obj[prop]` 같은 연산을 함수형으로 다룰 수 있다.
    - 프록시와 함께 쓸 때 훨씬 강력하다.
 
<br/>

### 프록시

- 객체의 **동작 자체를 가로채서(intercept)** 감시하거나 수정할 수 있는 메커니즘

```tsx
new Proxy(target, handler)
```

- `target`: 감시할 원본 객체
- `handler`: `get`, `set`, `has`, `deleteProperty` 등 트랩(trap)을 정의한 객체
    
    
    | 트랩 메서드 | 설명 |
    | --- | --- |
    | `get` | 속성 접근 감지 (`proxy.foo`) |
    | `set` | 속성 설정 감지 (`proxy.foo = 123`) |
    | `has` | `in` 연산자 감지 (`'foo' in proxy`) |
    | `deleteProperty` | 속성 삭제 감지 (`delete proxy.foo`) |
    | `ownKeys` | `Object.keys()`나 `for...in` 감지 |
    | `apply` | 함수 호출 감지 |
    | `construct` | `new` 연산자 감지 |
- 프록시+리플렉션 예시
    - **Proxy는 동작을 감시하거나 수정하고, Reflect는 안전하게 그 동작을 위임할 수 있는 도구이다.**
    
    ```tsx
    const target = { name: 'Alice' };
    
    const proxy = new Proxy(target, {
      get(target, prop, receiver) {
        console.log(`GET ${prop}`);
        return Reflect.get(target, prop);
      },
      set(target, prop, value, receiver) {
        console.log(`SET ${prop} = ${value}`);
        return Reflect.set(target, prop, value);
      }
    });
    
    proxy.name;        // 콘솔: GET name → 'Alice'
    proxy.age = 30;    // 콘솔: SET age = 30
    ```
     
<br/>


### 메타 프로그래밍이 유용한 사례

| 상황 | 설명 |
| --- | --- |
| **접근 제어** | JavaScript에 `private` 키워드가 없던 시절, 프록시로 숨김 구현 가능 |
| **Vue 2** | `Proxy` 없이 `Object.defineProperty`로 반응형 시스템 구성 |
| **Vue 3** | `Proxy`로 반응형 감지 (`reactive`, `ref`) |
| **API 유연화** | 속성 자동 생성, API 인터페이스 유연하게 조정 가능 |
| **로깅/디버깅** | 객체 동작을 추적하거나 디버깅 가능 |
| **ORM/데이터바인딩** | DB 모델 객체의 동작을 가로채서 쿼리 실행 등 구현 가능 |