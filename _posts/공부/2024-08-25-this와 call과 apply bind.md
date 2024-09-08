---
layout: post
title: this와 call과 apply bind
categories: 공부
tag: [this, 바인딩, call, apply, bind, javascript]
toc: true
---

# call apply bind

- this를 binding하는 방법으로 call, apply, bind 이렇게 3가지 메서드가 존재한다.
- javascript에서 this와 동적으로 바인딩할 수 있는 call과 apply bind에대해 알아보자.

## this와 바인딩이란

- this는 인스턴스의 자신을 가리키는 참조변수이다.
- 하지만 javascript에서 this는 함수의 호출 방식에 따라 this에 바인딩 되는 객체가 달라진다.
  - 정적으로 this가 결정되지 않고 동적으로 결정된다.
- 함수, 메서드, 생성자, 화살표(arrow)에서의 this를 알아보자.

### 함수에서의 this

- 함수내에서 this는 전역 객체에 바인딩된다.
  - 브라우저에서는 window, node.js에서는 global객체를 출력한다.

```js
function showThis() {
  a = 11;
  console.log(this); // 브라우저에서는 window, Node.js에서는 global
}
b = 12;

showThis();

/* 실제 출력 내용

<ref *1> Object [global] {
  global: [Circular *1],
  queueMicrotask: [Function: queueMicrotask],
  clearImmediate: [Function: clearImmediate],
  setImmediate: [Function: setImmediate] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  structuredClone: [Getter/Setter],
  clearInterval: [Function: clearInterval],
  clearTimeout: [Function: clearTimeout],
  setInterval: [Function: setInterval],
  setTimeout: [Function: setTimeout] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  atob: [Getter/Setter],
  btoa: [Getter/Setter],
  performance: [Getter/Setter],
  fetch: [AsyncFunction: fetch],
  crypto: [Getter],
  b: 12, //b가 함수 외부에서 선언되었어도, 함수내의 this에서 출력된다.
  a: 11
} 
*/
```

- 변수 b가 함수 외부이지만, this에서 출력이 되고 있는것을 볼 수 있다.

### 메서드에서의 this

- 객체의 메서드로 호출시 this는 해당 객체를 가리킨다.

```js
const test = {
  name: "test",
  showThis() {
    console.log(this);
  },
};

test.showThis();
/*출력 내용
{ name: 'test', showThis: [Function: showThis] }
 * /

```

### 생성자에서의 this

- es6의 class와 생성자 함수에서의 this는 생성된 객체를 가리킨다.

```js
class Person {
  constructor(name) {
    this.name = name;
  }
  //프로토타입에 정의
  showThis() {
    console.log(this);
  }
}

const alice = new Person("Alice");

alice.showThis();
/*
Person { name: 'Alice' }
*/
```

#### 프로토타입과 인스턴스 메서드의 차이

- 위의 클래스를 선언하는데, name은 표시되지만, showThis가 표시 안되고 있다.
- 하지만 아래처럼 코드를 작성하면 showThis가 표시되는것을 볼 수 있다.

```js
class Person {
  constructor(name) {
    this.name = name;
  }
  //인스턴스 메서드 방식
  showThis = function () {
    console.log(this);
  };
}

const alice = new Person("Alice");

alice.showThis();
/*
Person { showThis: [Function: showThis], name: 'Alice' }
*/
```

- 이 둘의 차이는 프로토타입이냐, 인스턴스 메서드냐로 나뉜다.
- 프로토타입
  - showThis이라는 메서드가 프로토타입에 정의되게 된다.
  - `Person.prototype.showThis`와 같은 형태가 된다.
  - 그래서 인스턴스 객체에 종속되지 않게 되며, 인스턴스 자체 속성이 아니게 된다.
- 인스턴스 메서드
  - 인스턴스 속성으로 정의된다.
    - 그래서 Person이 만들어질때마다, showThis라는 메서드가 직접 추가되는 형태
    - 익명의 함수로 만들고, showThis라는 변수에 저장된다.
- 두가지 방식의 차이는 메모리 사용에 있다.
  - 인스턴스 메서드는 showThis를 직접 추가하면서 인스턴스가 가져가게 된다.
  - 그래서 메모리 적으로 낭비가 될 수 있다.

### 화살표(arrow)함수에서의 this

- 화살표 arrow함수라고 불리는 `=>`로 만들어진 함수
- 화살표함수는 자신의 this를 가지지 않는다.
- 해당 함수가 정의된 위치의 상위 스코프에 영향을 받는다.

```js
const test = {
  name: "Alice",
  showThis: function () {
    const arrowFunc = () => {
      console.log(this); // obj 객체
    };
    arrowFunc();
  },
};
test.showThis();
/*
{ name: 'Alice', showThis: [Function: showThis] }
*/
```

## 동적으로 바인딩

- this를 동적으로 바인딩도 가능하다.
- this를 명시적으로 설정할 수 있으며, 함수 호출 맥락에 상관 없이 원하는 this를 바인딩 할 수 있다.

### call()

- 함수 호출시 this를 지정, 인자들은 `개별적`으로 전달

```js
function test(a, b) {
  console.log(this, a, b);
}

test.call({ k: 0 }, 1, 2);
```

### apply()

- 함수 호출시 this를 지정, 인자들은 `배열`로 전달
  - call과 차이는 배열로 전달된다는 차이가 존재

```js
function test(a, b) {
  console.log(this, a, b);
}

test.call({ k: 0 }, [1, 2]);
```

### bind()

- this를 지정한 새로운 함수를 반환

```js
function test(a, b) {
  console.log(this, a, b);
}

const bindTest = test.bind({ k: 0 }); //this를 고정해서 bindTest라는 함수를 생성
bindTest(1, 2);
```

## 참고

- https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-Call-Bind-Apply#apply
- https://poiemaweb.com/js-this
- https://www.zerocho.com/category/JavaScript/post/57433645a48729787807c3fd
- https://velog.io/@dev_0livia/JavaScriptcall-apply-bind%EC%9D%98-%EC%B0%A8%EC%9D%B4
- https://klmhyeonwooo.tistory.com/61
- https://velog.io/@do_dam/JavaScript-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98Constructor-function%EC%99%80-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85Prototype
