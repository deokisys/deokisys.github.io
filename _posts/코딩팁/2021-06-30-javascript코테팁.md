---
layout: post
title: javascript배열 팁
categories: 코딩테스트
tags: [코딩테스트,배열,javascript]
toc : true
math : true
---


### 배열만들기
- 기본적인 배열만들기
```js
let a = []
let a = Array(/*배열갯수*/)
```

- 채워진 배열 만들기
```js
let a = Array(10).fill(/*넣을것*/)

```

- 이중배열!
```js
//undefined로 이루어진 10*10짜리 배열이 만들어진다.
//let a = Array(10).fill(Array(10).fill(undefined))  잘못된 방식! 같은 배열을 만들게 된다.
let a = []
for(let i=0;i<10;i++){
    a.push(Array(10).fill(undefined))
}
```

### 배열마다 번호붙이기
- map을 활용하여 enumerate효과를 준다.
  
```js
let b = ['a','b','c','d','e']
let a = b.map((ele,i)=>[...ele,i])
//a  = [['a',0],['b',1],['c',2],['d',3],['e',4]]
```