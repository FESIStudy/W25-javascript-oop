# 객체지향프로그래밍

객체 지향 프로그래밍(OOP)은 프로그램을 객체(object)들로 구성하여 설계하고 개발하는 방법이라고 합니다.

특징

- 다형성(Polymorphism): 동일한 메서드가 다양한 객체에서 다르게 동작.
- 캡슐화(Encapsulation): 객체의 내부 상태를 숨기고, 메서드를 통해 접근.

이 특징들은 뭘 의미하는걸까

## 다형성

```js
// 기본 클래스
class Animal {
  makeSound() {
    return '동물이 소리를 냅니다';
  }
}

class Dog extends Animal {
  makeSound() {
    return '멍멍!';
  }
}

class Cat extends Animal {
  makeSound() {
    return '야옹!';
  }
}

class Duck extends Animal {
  makeSound() {
    return '꽥꽥!';
  }
}

// 다형성 활용
const animals = [new Dog(), new Cat(), new Duck()];

animals.forEach((animal) => {
  console.log(animal.makeSound()); // 각각 다른 소리가 출력됨
});
// 출력: "멍멍!", "야옹!", "꽥꽥!"
```

같은 makeSound함수를 호출하지만 결과가 다다름

## 캡슐화

클래스 내부 선언된(보통 프라이빗 변수) 변수에 직접 접근 안되고 getter함수로 값을 가져오는것.

## 클래스 사용예시

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} makes a noise.`);
  }
}

class Dog extends Animal {
  constructor(name) {
    super(name);
  }
  speak() {
    console.log(`${this.name} barks.`);
  }
}

let dog = new Dog('Rex');
dog.speak(); // Rex barks.
```

여기서 궁금했던거
this.name = name; 이거 name필드 선언도 안했는데 어떻게 가능한거냐?
-> js에서는 동적으로 프로퍼티가 생성이 가능하다고 한다. // 개인적으로는 코드 보기 더 불편해서 싫음

super가 뭐였더라?
-> 상속해준 부모 클래스의 생성자함수 실행시키는거.

# this

보통 클래스 객체를 사용해서 함수를 짜거나 할때, `this`이걸 많이 쓰는데. 이번에 개념 확실히 잡고가자.

간단히 이해하면 this는 `지금 누가 이 함수를 실행하고 있는가`를 의미한다.

```js
const person = {
  name: '홍길동',
  sayHello: function () {
    console.log('안녕, 나는 ' + this.name + '이야');
    //                    ↑ 여기서 this는 person을 가리킴
  },
};

person.sayHello(); // "안녕, 나는 홍길동이야"

// "person 객체야, 너의 sayName 메서드를 실행해줘"
// → this = person
// → this.name = person.name = '홍길동'
```

여기서 sayHello는 person객체가 호출한거 -> 그럼 this는 person객체

가끔 this가 내 에상이랑 다르게 고정될때가 있는데 바로 이럴떄

```js
const person = {
  name: '홍길동',
  sayName: function () {
    console.log(this.name);
  },
};

person.sayName(); // "홍길동" (this = person)

const sayName = person.sayName;
sayName(); // undefined (this = 전역객체, name 프로퍼티 없음)
```

어랍쇼 내가볼땐 둘다 홍길동이 나와야하는데 왜 이렇게 나오는거임?

person.sayName();이거는 함수을 호출한 애가 person이니까 당연히 this가 person으로 설정

const sayName = person.sayName;
sayName();

이거는 그렇다면 sayName()이 될떄 누가 이 함수를 호출했는가를 봐야하는데
const sayName = person.sayName;이 코드에서 this까지 복제되는게 아니라
그냥 sayName함수 자체만 복사되는거임 그래서 이렇게 되는거임

```js
// "그냥 sayName 함수를 실행해줘"
// → this = 전역객체 (브라우저에서는 window)
// → this.name = window.name = undefined
```

그러면 그냥 함수에서 this를 쓴다면?

```js
function normalFunction() {
  console.log(this); // 브라우저: window, Node.js: global
}

normalFunction(); // 전역 객체를 가리킴
```

그러면 이때 this를 내가 원하는 객체로 덮어쓸 수 있을까?

## bind, call/apply

```js
const obj1 = { name: '객체1' };
const obj2 = { name: '객체2' };

function introduce() {
  console.log('나는 ' + this.name);
}

introduce.call(obj1); // "나는 객체1" (this = obj1)
introduce.call(obj2); // "나는 객체2" (this = obj2)
```

여기서 call은 해당 함수를 호출하되, 첫 번째 인자로 전달된 객체를 this로 바인딩합니다.

디테일한 예시

```js
function greet(greeting: string, punctuation: string) {
  console.log(`${greeting}, 나는 ${this.name}${punctuation}`);
}

const person1 = { name: '철수' };
const person2 = { name: '영희' };

greet.call(person1, '안녕', '!'); // 안녕, 나는 철수!
greet.call(person2, '반가워', '~'); // 반가워, 나는 영희~
```

apply도 비슷한데 얘는 파라미터를 어레이로 받음

```js
greet.apply(person1, ['안녕', '!']); // 안녕, 나는 철수!
greet.apply(person2, ['반가워', '~']); // 반가워, 나는 영희~
```

bind는 this를 인자로 받은 객체로 설정한 후에 그 함수를 반환함

```js
const greetChulsoo = greet.bind(person1, '안녕');
greetChulsoo('!!!'); // 안녕, 나는 철수!!!

const greetYounghee = greet.bind(person2);
greetYounghee('안녕', '?'); // 안녕, 나는 영희?
```

bind는 바로 실행되지 않고, 고정된 this를 갖는 새로운 함수를 반환합니다.

## 화살표 함수는 상위 스코프의 this를 그대로 사용

```js
const obj = {
  name: '객체',
  regularFunction: function () {
    console.log('일반함수:', this.name); // "객체"

    const arrowFunction = () => {
      console.log('화살표함수:', this.name); // "객체" (상위 스코프의 this 사용)
    };
    arrowFunction();
  },
};

obj.regularFunction();
```

우리는 아까 일반함수에서 this를 쓰면 전역객체가 this로 설정되는걸 봤다.

```js
const arrowFunction = () => {
  console.log('화살표함수:', this.name); // "객체" (상위 스코프의 this 사용)
};
```

그럼 이것도 this가 전역객체고 name필드에 설정을 한게 없으니 undefined가 나올까요?
아니다. 화살표 함수는 상위 스코프의 this를 사용하기 때문에
여기에선 저 화살표 함수의 상위 스토프 즉 obj객체가 this로 사용되어서
'객체'라는 값이 this.name으로 쓰이는것.

# Proxy

클래스를 생성할때 직접 getter, setter를 짜는게 아니라 프록시 객체를 둬서 커스터마이징되는것도 있더라?

Proxy는 객체의 동작을 가로채서 커스터마이징할 수 있는 기능

프락시 기본 개념

```js
const proxy = new Proxy(target, handler);
// target: 원본 객체
// handler: 어떤 동작을 가로챌지 정의하는 객체
```

```js
// 1. 원본 객체 생성
const user = {
  name: 'Alice',
  age: 25,
};

// 2. 쁘락치 생성 & 핸들러 정의
const proxy = new Proxy(user, {
  get(target, property) {
    console.log(`Getting ${property}`);
    return target[property];
  },
  set(target, property, value) {
    console.log(`Setting ${property} to ${value}`);
    target[property] = value;
    return true;
  },
});

console.log(proxy.name); // Getting name \n Alice
proxy.age = 26; // Setting age to 26
```

프락찌가 어떻게 핸들러를 조작했는지 뜯어보자.

console.log(proxy.name) 실행:

```js
proxy.name;
// ↓ get 트랩 실행
// target = user, property = 'name'
console.log(`Getting name`); // 출력: "Getting name"
return target['name']; // 'Alice' 반환
// ↓ 최종 출력
// "Alice"
```

proxy.age = 26 실행:

```js
proxy.age = 26;
// ↓ set 트랩 실행
// target = user, property = 'age', value = 26
console.log(`Setting age to 26`); // 출력: "Setting age to 26"
target['age'] = 26; // user.age = 26으로 설정
return true; // 성공 표시
```

그럼 이 쁘락찌를 어떨때 쓰냐. api짤때 가드같은 느낌으로 들어가보면 좋다.

Proxy에 대해 설명해드릴게요! Proxy는 **객체의 동작을 가로채서 커스터마이징**할 수 있는 강력한 기능입니다.

### 1. 데이터 검증

```javascript
const person = new Proxy(
  {},
  {
    set(target, property, value) {
      if (property === 'age' && (value < 0 || value > 150)) {
        throw new Error('나이는 0-150 사이여야 합니다');
      }
      if (property === 'name' && typeof value !== 'string') {
        throw new Error('이름은 문자열이어야 합니다');
      }

      console.log(`${property}를 ${value}로 설정`);
      target[property] = value;
      return true;
    },
  },
);

person.name = 'Bob'; // "name를 Bob로 설정"
person.age = 30; // "age를 30로 설정"
// person.age = -5;     // Error: 나이는 0-150 사이여야 합니다
```

### 2. 속성 접근 로깅

```javascript
const api = {
  users: ['Alice', 'Bob'],
  posts: ['게시글1', '게시글2'],
};

const loggedApi = new Proxy(api, {
  get(target, property) {
    console.log(`[${new Date().toISOString()}] ${property} 접근됨`);
    return target[property];
  },
});

console.log(loggedApi.users);
// [2024-01-01T12:00:00.000Z] users 접근됨
// ['Alice', 'Bob']
```

### 3. 기본값 제공

```javascript
const config = new Proxy(
  {},
  {
    get(target, property) {
      // 존재하지 않는 속성에 기본값 제공
      if (!(property in target)) {
        console.log(`${property}가 없어서 기본값 반환`);
        return 'default-value';
      }
      return target[property];
    },
  },
);

config.theme = 'dark';
console.log(config.theme); // "dark"
console.log(config.language); // "default-value가 없어서 기본값 반환" → "default-value"
```

### 4. 동적 속성 계산

```javascript
const calculator = new Proxy(
  {},
  {
    get(target, property) {
      if (property.startsWith('double_')) {
        const num = property.split('_')[1];
        return parseInt(num) * 2;
      }
      if (property.startsWith('square_')) {
        const num = property.split('_')[1];
        return parseInt(num) ** 2;
      }
      return target[property];
    },
  },
);

console.log(calculator.double_5); // 10
console.log(calculator.square_4); // 16
```

### 5. 배열 음수 인덱스 지원

```javascript
const arr = new Proxy(['a', 'b', 'c', 'd'], {
  get(target, property) {
    if (typeof property === 'string' && /^-\d+$/.test(property)) {
      // 뒤에는 음수 정규식
      // 음수 인덱스 처리
      const index = target.length + parseInt(property);
      return target[index];
    }
    return target[property];
  },
});

console.log(arr[0]); // 'a'
console.log(arr[-1]); // 'd' (마지막 요소)
console.log(arr[-2]); // 'c' (뒤에서 두 번째)
```

## 다른 트랩들

```javascript
const obj = new Proxy(
  {},
  {
    // 속성 존재 확인 (in 연산자)
    has(target, property) {
      console.log(`${property} 존재 확인`);
      return property in target;
    },

    // 속성 삭제 (delete 연산자)
    deleteProperty(target, property) {
      console.log(`${property} 삭제 시도`);
      delete target[property];
      return true;
    },

    // 속성 목록 조회 (Object.keys 등)
    ownKeys(target) {
      console.log('속성 목록 조회');
      return Object.keys(target);
    },
  },
);

obj.name = 'test';
console.log('name' in obj); // "name 존재 확인" → true
delete obj.name; // "name 삭제 시도"
console.log(Object.keys(obj)); // "속성 목록 조회" → []
```

## 실제 활용 사례

### Vue.js의 반응성 시스템 (간단 버전)

```javascript
function reactive(obj) {
  return new Proxy(obj, {
    set(target, property, value) {
      target[property] = value;
      console.log(`${property}가 변경됨 - UI 업데이트 필요!`);
      // 실제로는 여기서 DOM 업데이트 등을 수행
      return true;
    },
  });
}

const state = reactive({ count: 0 });
state.count++; // "count가 변경됨 - UI 업데이트 필요!"
```

**핵심 정리:**

- Proxy는 객체의 **모든 동작을 가로채서** 커스터마이징 가능
- `get`, `set` 외에도 `has`, `deleteProperty` 등 다양한 트랩 제공
- 데이터 검증, 로깅, 기본값 제공, 반응성 시스템 등에 활용
