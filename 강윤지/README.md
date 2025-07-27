https://wikidocs.net/251866

해당 내용의 1 ~ 8장을 읽고 정리한 내용입니다.

## 자바스크립트의 특징

### **인터프리터 언어**

- 컴파일 없이 브라우저에서 직접 해석: 자바스크립트 코드는 별도의 컴파일 과정 없이 브라우저에서 직접 실행됨.

### **동적 타이핑**

- **변수의 타입이 런타임에 결정됨**: 자바스크립트 변수는 선언 시 타입을 지정하지 않으며, 런타임에 타입이 결정됨.

### **객체 기반 언어**

- **모든 것이 객체로 취급됨**: 자바스크립트에서는 거의 모든 것이 객체로 취급되며, 프로토타입 기반 상속을 지원함.

### **함수형 프로그래밍 지원**

- **일급 함수 및 고차 함수 지원**: 자바스크립트는 함수를 일급 객체로 취급하여 함수형 프로그래밍 패러다임을 지원함.

### **비동기 프로그래밍**

- **이벤트 루프, 콜백, 프라미스, async/await 사용**: 자바스크립트는 비동기 프로그래밍을 위한 다양한 메커니즘을 제공하여, 비동기 작업을 쉽게 처리할 수 있게 함.

### **플랫폼 독립성**

- **웹 브라우저와 Node.js에서 동작**: 자바스크립트는 브라우저와 서버 환경 모두에서 실행 가능하여, 플랫폼 독립적으로 사용할 수 있음.

### **풍부한 생태계**

- **방대한 라이브러리와 프레임워크, NPM을 통한 패키지 관리**: 자바스크립트는 풍부한 생태계를 가지고 있으며, NPM(Node Package Manager)을 통해 쉽게 패키지를 관리하고 사용할 수 있음.

## 객체 지향 프로그래밍이란?

프로그램을 객체(Object)들로 구성하여 설계하고 개발하는 방법.

- **객체(Object)**: 데이터(속성)와 이를 조작하는 함수(메서드)의 집합.
- **클래스(Class)**: 객체를 생성하기 위한 청사진.
- **상속(Inheritance)**: 하나의 클래스가 다른 클래스의 속성과 메서드를 재사용.
- **다형성(Polymorphism)**: 동일한 메서드가 다양한 객체에서 다르게 동작.
- **캡슐화(Encapsulation)**: 객체의 내부 상태를 숨기고, 메서드를 통해 접근.

→ 자바스크립트는 프로토타입 기반 언어로, ES6 이후 클래스 문법도 지원함.

### 클래스

- 객체를 생성하기 위한 템플릿
- class 키워드로 정의, 생성자 함수와 메서드 선언 가능

```tsx
class Animal {
  constructor(name) {
    this.name = name; // 객체의 초기 상태를 설정하는 생성자 함수
  }
  speak() {
    console.log(this.name + " makes a noise."); // 클래스 내부의 메서드
  }
}
```

- extends 키워드로 상속 가능.
- super 키워드로 부모 클래스의 생성자 호출 가능

```tsx
class Dog extends Animal {
  constructor(name) {
    super(name); // 부모 클래스의 생성자를 호출하여 name을 초기화
  }
  speak() {
    console.log(this.name + " barks."); // 부모 클래스의 메서드를 재정의 (오버라이딩)
  }
}
```

### 프로토타입

- 자바스크립트의 객체 지향 프로그래밍을 구현하는 방식 중 하나.
- 모든 객체는 자신의 부모 역할을 하는 프로토타입 객체를 가진다.
- 프로토타입을 통해 객체는 상속을 받아 다른 객체의 속성과 메서드 사용 가능.

```tsx
function Animal(name) {
  this.name = name; // 생성자 함수
}

Animal.prototype.speak = function () {
  console.log(this.name + " makes a noise."); // 프로토타입 메서드
};

let animal = new Animal("Rex");
animal.speak(); // Rex makes a noise.
```

- Animal.prototype에 speak 메서드를 추가하면 모든 Animal 인스턴스가 이 메서드 사용 가능
- 상속과 확장
  ```tsx
  function Dog(name) {
    Animal.call(this, name); // Animal 생성자 함수 호출
  }

  Dog.prototype = Object.create(Animal.prototype); // Animal 프로토타입을 상속받음
  Dog.prototype.constructor = Dog; // Dog.prototype.constructor를 Dog로 설정

  Dog.prototype.speak = function () {
    console.log(this.name + " barks."); // 메서드 재정의(오버라이딩)
  };

  let dog = new Dog("Rex");
  dog.speak(); // Rex barks.
  ```
  - Dog 생성자 함수는 Animal 생성자를 호출하여 name 속성을 초기화 한다.
  - Dog.prototype은 Animal.prototype을 상속받아 speak 메서드를 사용할 수 있고, 재정의가 가능하다.
  - → `Object.create`와 `call`을 사용하여 프로토타입 상속과 메서드 재정의 구현

### 클래스 vs 프로토타입

|           | 클래스                                                                       | 프로토타입                                  |
| --------- | ---------------------------------------------------------------------------- | ------------------------------------------- |
| 정의      | 객체를 생성하기 위한 청사진                                                  | 다른 객체를 템플릿으로 사용하여 객체를 생성 |
| 도입 버전 | ES6(ECMAScript 2015)에서 도입                                                | 자바스크립트 초기 버전부터 존재             |
| 상속      | 명시적 상속 구조로 부모 클래스로부터 자식 클래스가 속성과 메서드를 상속받음. | 객체 간의 직접적인 링크를 통해 상속         |
| 구문      | class 키워드를 사용하여 정의                                                 | Object.create를 사용하여 구현               |
| 장점      | 명확한 구조, 캡슐화, IDE 지원                                                | 유연성, 메모리 효율성                       |
| 단점      | 유연성 부족, 복잡성                                                          | 혼란 가능성, 지원 부족                      |
| 사용 예시 | 대규모 애플리케이션                                                          | 동적 웹 애플리케이션                        |

### 믹스인(Mixin)

- 클래스나 객체에 공통 기능을 혼합하여 재사용성을 높이는 디자인 패턴.
- 상속을 사용하지 않고도 여러 클래스에 공통 기능을 추가할 수 있게 해준다.
- 클래스 기반 믹스인
  ```jsx
  Copylet sayHiMixin = {
  sayHi() {
            console.log(`Hello, ${this.name}`);
        },
  sayBye() {
            console.log(`Bye, ${this.name}`);
        }
    };

  classUser {
  constructor(name) {
            this.name = name;
        }
    }

    // 메서드 복사
  Object.assign(User.prototype, sayHiMixin);

  const user =newUser('Alice');
    user.sayHi(); // Hello, Alice
    user.sayBye(); // Bye, Alice
  ```
- 프로토타입 기반 믹스인
  ```jsx
  const sayHiMixin = {
  sayHi() {
            console.log(`Hello, ${this.name}`);
        },
  sayBye() {
            console.log(`Bye, ${this.name}`);
        }
    };

  functionUser(name) {
        this.name = name;
    }

  Object.assign(User.prototype, sayHiMixin);

  const user =newUser('Alice');
    user.sayHi(); // Hello, Alice
    user.sayBye(); // Bye, Alice
  ```

### 다형성

객체 지향 프로그래밍에서 동일한 인터페이스를 통해 서로 다른 데이터 타입을 처리할 수 있게 하는 특성. 주로 상속과 인터페이스를 통해 구현.

```jsx
classAnimal {
speak() {
          console.log('Animal speaks');
      }
  }

classDogextendsAnimal {
speak() {
          console.log('Dog barks');
      }
  }

classCatextendsAnimal {
speak() {
          console.log('Cat meows');
      }
  }

functionmakeAnimalSpeak(animal) {
      animal.speak();
  }

const dog =newDog();
const cat =newCat();

makeAnimalSpeak(dog); // Dog barks
makeAnimalSpeak(cat); // Cat meows
```

이처럼 동일한 makeAnimalSpeak 라는 함수에서 dog / cat 인지에 따라 다르게 처리할 수 있다.

### 메타프로그래밍

프로그램이 자신을 수정하거나 새로운 행동을 추가하는 기술. 자바스크립트에서는 리플렉션(Reflection)과 프로시(Proxy)를 통해 구현된다.

- **리플렉션(Reflection)**

```lua
  const user = {
      name: 'Alice',
      age: 25
  };

  console.log(Object.keys(user)); // ['name', 'age']
  console.log(Object.values(user)); // ['Alice', 25]
  console.log(Object.entries(user)); // [['name', 'Alice'], ['age', 25]]
```

- **프록시(Proxy)**

```jsx
Copyconst user = {
      name: 'Alice',
      age: 25
  };

const proxy =newProxy(user, {
get(target, property) {
          console.log(`Getting ${property}`);
return target[property];
      },
set(target, property, value) {
          console.log(`Setting ${property} to ${value}`);
          target[property] = value;
return true;
      }
  });

  console.log(proxy.name); // Getting name \n Alice
  proxy.age = 26; // Setting age to 26
```

### 정리

클래스와 프로토타입의 장단점을 이해하고, 프로젝트 요구사항에 맞게 적절히 활용하는 것이 중요.
