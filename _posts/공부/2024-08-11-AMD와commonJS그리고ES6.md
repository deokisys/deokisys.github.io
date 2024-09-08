---
layout: post
title: AMD와commonJS그리고ES6
categories: 공부
tag: [amd, commonjs, es6, 모듈]
toc: true
---

# amd, coomonjs, es6

- javascript를 사용하다보면 자주 보이는 용어이며, 어떤 차이가 있는지 잘 모르는 경우가 많다.
- 이를 모듈이라 부르며 모듈시스템의 종류의 차이를 정리해본다.
  - 종류로는 AMD, CommonJS, UMD, ES Module(es6)가 존재한다.
  - import/export, require/exports등 사용하는 방식이 존재한다.

## CommonJS

- javascript가 브라우저 뿐만 아니라 node를 통해 서버에서 사용하면서 node에서 채택한 방식
- 서버에서 사용하므로 순서에 맞게 작동한다.
- node.js를 사용하면 이 모듈 시스템을 사용하고 있다.
- 내보낼때는 `exports`, `module.exports` 모듈을 불러올때는 required를 사용

```js
//hello.js
const outText = "hello";

module.exports = { outText };

//app.js
const hello = require("./hello.js");
console.log(hello.outText); //hello가 출력됨
```

### 특징

- 서버사이드에서 사용하기 위해 만들어졌다.
- 로컬 디스크에서 모듈을 로드할때 더 빠르고 간결하다.
- 모듈파일마다 스코프가 설정
- 파일 덩어리를 가져오지 않고 필요한 함수와 변수를 가져 올 수 있다.
- 브라우저 관점에서 단점이 존재
  - 각 파일마다 독립적인 스코프가 브러우저에서는 없어지기 때문에 전역 변수가 오염된다고 한다.
  - blocking방식이라 너무 느리다

### module.exports와 exports 뭔차이일까

- `exports`, `module.exports` 둘다 node.js에서 모듈을 내보낼때 사용한다.
- 내부적으로는 아래와 같은 형태로 되어있다고 한다.
  - `const exports = module.exports;`
- exports는 module.exports의 별칭으로 빈객체({})로 초기화된 상태이다.
  - 결국 같은 말인거 같은데 뭔 차이가 있을까?
- 큰 차이점은 `객체에 직접 할당`할때 발생한다.
- 아래처럼 module.exports에 직접 할당하는 예를 보자

```js
// myModule
module.exports = function () {
  console.log("Hello from module.exports");
};

//
const myModule = require("./myModule");
myModule();
```

- 이 예시를 보면 바로 require를 한다음 함수로서 사용이 가능한것을 볼 수 있다.

- exports의 경우에는 아래처럼 사용이 가능하다.

```js
// myModule
exports.sayHi = function () {
  console.log("Hello from module.exports");
};

//
const myModule = require("./myModule");
myModule.sayHi();
```

- exports내에서는 {sayHi:function}과 같은 형태로 사용되는것을 알 수 있다.
- 그렇다고 exports를 module.exports와 같이 사용하지는 못한다는 점을 유의합시다.

```js
//잘못된예
exports = function () {};
```

## AMD

- Acynchronous Module Definition의 약자
- 비동기인 브라우저환경에서 모듈을 사용히기 위해 따로 독립한 그룹이며 비공식 모듈 시스템이다.
- `Require.js`라는 라이브러리로 AMD 스펙을 구현했다.
- define으로 모듈을 내보내고, require로 모듈을 받는다.
  - define의 리턴이 exports속성이 되는 형태로 만들어져 있다고 한다.
- es6이전 구형프로젝트에선 비동기를 위해 사용되었지만, 이제는 ES Module로 표준화된 모듈 시스템으로 구현되고 있다.

## ES6/ES Module/ESM

- ES Modlue은 ES6에서부터 도입된 자바스크립트 모듈 시스템
  - 좀더 브라우저에서도 쉽게 모듈화가 되도록 도입되었다.
- 내보낼때는 `export`, `default export`를 사용하고, 가져올때는 `import`를 사용한다.
- 브라우저 스크립트에서는 `<script type="module">`을 통해서 ES6기반 모듈을 사용할 수 있게 된다.
  - 그리고 이런 파일에 대해서는 mjs라는 확장자로 설정하도록 권장된다.
  - 권장이므로 js로도 작동은 된다.
  - 위처럼 script에 type을 적용하지 않으면 import/export가 작동하지 않는다.
- default를 같이 사용하게 되면 아래처럼 바로 import하고 사용하는게 가능하다.

```js
//foo.js
function foo() {
  console.log("foo");
}

export default { foo };

//main.js
import foo from "foo";
foo();
```

### 특징

- commonjs방식에 비해 코드의 직관성이 좋다.
- 비동기 방식으로 작동하여 실제 사용하는 부분만 로드하기 때문에 성능적으로 효율적이다.
- 아직까지는 webpack과 같은 모듈 번들러를 이용하여 의존성이 해결된 번들 파일을 제공하는 방식이 선호한다.
- 일부 구형 브라우저는 지원하지 않는다.

## 참고

- https://gobae.tistory.com/130
- https://velog.io/@dae_eun2/Module-CommonJs-%EC%99%80-Es6-%EC%B0%A8%EC%9D%B4
- https://it-eldorado.tistory.com/92
- https://pul8219.github.io/javascript/js-module/
