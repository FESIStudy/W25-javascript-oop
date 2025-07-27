# JS OOP
객체들로 구성하여 설계하고 개발하는 방법
## 주요 개념
- 객체(Object): 데이터(속성)와 이를 조작하는 함수(메서드)의 집합.
- 클래스(Class): 객체를 생성하기 위한 청사진.
- 상속(Inheritance): 하나의 클래스가 다른 클래스의 속성과 메서드를 재사용.
- 다형성(Polymorphism): 동일한 메서드가 다양한 객체에서 다르게 동작.
- 캡슐화(Encapsulation): 객체의 내부 상태를 숨기고, 메서드를 통해 접근.


## 클래스 타입과 프로토타입 비교
### 클래스
- 구문: class 키워드를 사용하여 정의
- 버전: ES6 에서 도입됨
- 상속: 부모 클래스로부터 자식 클래스가 속성과 메서드를 상속받음
```js
class Animal {
    constructor(name) {
        this.name = name;
    }
    speak() {
        console.log(this.name + ' makes a noise.');
    }
}

class Dog extends Animal {
    constructor(name) {
        super(name);  // 부모 클래스의 생성자를 호출하여 name을 초기화
    }
    speak() {
        console.log(this.name + ' barks.');  // 부모 클래스의 메서드를 재정의 (오버라이딩)
    }
}

let dog = new Dog('Rex');
dog.speak();  // Rex barks.
```


### 프로토타입
- 구문: Object.create를 사용하여 구현
- 버전: 자바스크립트 초기버전부터 존재
- 상속: 객체 간의 직접적인 링크를 통해 상속
```js
//프로토타입 형태
function Animal(name){
  this.name = name;
}
Animal.prototype.getName = function (){
  console.log(this.name)
}
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
- 프로토타입: 객체 지향 프로그래밍을 구현하는 자바스크립트의 방식으로, 객체는 자신의 프로토타입 객체를 통해 상속받습니다.
- 기본 문법: 생성자 함수와 prototype 속성을 사용하여 객체와 메서드를 정의합니다.
- 상속과 확장: Object.create와 call 메서드를 사용하여 프로토타입 상속과 메서드 재정의를 구현합니다.

### 각각의 장단점
클래스
**장점**
- 명확한 구조: 클래스 기반 코드는 구조가 명확하고 이해하기 쉬움.
- 캡슐화: 클래스 내부에서만 접근 가능한 비공개 멤버를 정의할 수 있음.
- IDE 지원: 강력한 코드 자동 완성 및 정적 분석 지원.
**단점**
- 유연성 부족: 런타임에서 클래스 구조를 변경하기 어려움.
- 복잡성: 깊은 상속 계층이 코드의 복잡성을 증가시킬 수 있음.

프로토타입 
**장점**
- 유연성: 런타임에서 객체의 구조를 동적으로 변경할 수 있음.
- 메모리 효율성: 공통 메서드는 단일 프로토타입 객체에 저장되어 메모리 사용이 효율적임.
**단점**
- 혼란 가능성: 프로토타입 체인은 디버깅을 어렵게 하고 코드의 예측 가능성을 낮출 수 있음.
- 지원 부족: 일부 IDE는 프로토타입 기반 언어에 대한 지원이 제한적일 수 있음.


## 믹스인, 다형성, 메타 프로그래밍 패턴
### 믹스인
믹스인은 클래스나 객체에 공통 기능을 혼합하여 재사용성을 높이는 디자인 패턴, **상속을 사용하지 않고 여러 클래스에 공통기능을 추가**할 수 있게 해줍니다.
```js
let sayHiMixin = {
      sayHi() {
          console.log(`Hello, ${this.name}`);
      },
      sayBye() {
          console.log(`Bye, ${this.name}`);
      }
  };

class User {
    constructor(name) {
        this.name = name;
    }
}

/*
프로토타입 기반
  Object.assign(User.prototype, sayHiMixin);
*/

Object.assign(User.prototype, sayHiMixin);

const user = new User('Alice');
user.sayHi(); // Hello, Alice
user.sayBye(); // Bye, Alice
```

### 다형성
동일한 인터페이스를 통해 서로 다른 데이터 타입을 처리하는 특성입니다. 주로 상속과 인터페이스를 통해 구현

```js
class Animal {
    speak() {
        console.log('Animal speaks');
    }
}

class Dog extends Animal {
    speak() {
        console.log('Dog barks');
    }
}

class Cat extends Animal {
    speak() {
        console.log('Cat meows');
    }
}

function makeAnimalSpeak(animal) {
    animal.speak();
}

const dog = new Dog();
const cat = new Cat();

makeAnimalSpeak(dog); // Dog barks
makeAnimalSpeak(cat); // Cat meows
```

### 메타프로그래밍


- 리플렉션
```js
const user = {
    name: 'Alice',
    age: 25
};

console.log(Object.keys(user)); // ['name', 'age']
console.log(Object.values(user)); // ['Alice', 25]
console.log(Object.entries(user)); // [['name', 'Alice'], ['age', 25]]
```

- 프록시
```js
const user = {
    name: 'Alice',
    age: 25
};

const proxy = new Proxy(user, {
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
### 요약
 자바스크립트에서 OOP를 구현하는데 사용되는 중요한 개념들입니다. 개념들은 코드의 재사용성과 유지보수, 동적인 기능 추가를 가능하게 합니다.
