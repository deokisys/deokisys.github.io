---
layout: post
title: 순수js웹에 react적용기 - redux
categories: 웹
tags: [react, javascript, webpack, babel, redux]
toc: true
math: true
---

# javascript의 설계 문제
- 순수 javascript로 프로젝트를 진행하면서 만들어진 구조를 해결하기 위해 도입하였다.


## 리액트 결과

- before
![리액트처리전](https://github.com/deokisys/deokisys.github.io/assets/24247768/59ec8548-4ccd-4ad7-98b7-207e331a7eff)

- after
![리액트처리](https://github.com/deokisys/deokisys.github.io/assets/24247768/daa566ff-77f8-4fe0-9ba6-8112b92a4371)

- 두 차이는 주소창 우측에 react devtool이 보인다. 
- 이전에는 색 표시도 안되지만, react를 적용하고나서는 파란색의 리액트표시가 나오는것을 볼 수 있다.

- 겉보기에는 큰 차이가 없지만, 내부 라이브러리가 변경된것이다.
  - 직접 두 사이트를 들어가서 확인해볼수 있도록 하였다.
  - [react적용전 - loaold.deokisys.site](http://loaold.deokisys.site/)
  - [react적용 - loa.deokisys.site](http://loa.deokisys.site/)

- 어떤 문제가 있었고, 어떻게 해결했는지 설명한다.


## 설계 문제

- 순수 javascript로 spa를 구현하려 했을때 문제는 새로운 dom의 추가이다.
- react는 <></>를 return하는 간단한 구조지만, js로 만들때 일일히 createDom을 이용하였다.


### 바닐라 javascript의 구조
- 각 함수내에서는 element로 div를 만들어내고 return하는 방식으로 진행하였다.

```js
export default function harbContent(){
    let div = document.createElement("div")
    div.classList.add("harb")

    let mapVoid  = document.createElement("div")
    mapVoid.classList.add("harbCount")

    //배치를 위한 빈공간
    div.appendChild(mapVoid)
    //컨트롤러
    let [contDiv,panelBox,timerBox,infoBox] = controller()
    div.appendChild(contDiv)
    //맵
    let [mapDiv,mapBox] = harbMap()
    div.appendChild(mapDiv)
    
    //로직
    goAction(panelBox,mapBox,timerBox,infoBox)
    return div
}
```


### react로 만들기

- 이런 부분은 react화할때 큰 문제가 없었다.

```js

...
return (
  <div className="harb">
    <div className="harbCount"></div>
    <HarbController/>
    <HarbMap />
  </div>
);

```

- 위에서 createElement한 그대로 만들어주기만 하면 되었다.
- 더 생각해야 하는것은 해당 컴포넌트로 인자를 넘기는것은 된다.(부모에서 자식으로 전달 O) 
- 하지만 해당 컴포넌트에게서 return을 받지 못한다는것이다.(자식에서 부모로 전달 X)
- 이런 상태 관리 문제가 컸다.

## 상태 관리 문제

- js에서는 상태를 위해 하나의 싱글톤 객체를 만들거나, 전역변수를 만들었다.
- react로 넘어가면서 useState, useEffect를 쓰면서 생긴 문제와 해결을 작성해보았다.



### 기존 구조

- 해당 프로젝트에서는 각 element구조를 직접적으로 다루고 있었다.
  - createElement를 받아서 dom 조작을 진행했다.

```js

let [contDiv,panelBox,timerBox,infoBox] = controller()
div.appendChild(contDiv)
let [mapDiv,mapBox] = harbMap()
div.appendChild(mapDiv)
...

goAction(panelBox,mapBox,timerBox,infoBox)

```

- 위를 보면 panelBox,timerBox,infoBox,mapBox라는 객체를 받고있다.
- 그리고 컴포넌트라 할수있는 countDiv, mapDiv도 받아서 appendChild를 하고있다.
  - countDiv는 하브렐의 버튼들이 모여있는부분, mapDiv는 하브렐의 지도 컴포넌트이다.
- 가장 큰 문제가 이 ~Box들이다.

- mapBox를 예시로 보자

```js
//선언
let mapBox = {}

...

//harbSector로부터 action을 받아서 넣는다.
let [sec,action] = harbSector(hps[index])
mapBox[id] = action

...//harbSector의 action
let action = (atk)=>{
  //섹터에 atk만큼 데미지를 입히는 로직
}

//mapBox 사용
mapBox[id](dmg)

```

- 큰문제가 없어보인다.
  - 당연하다 진짜 문제없이 잘돈다.
  - 하지만 react에서는 문제가 생긴다.


### react로 하면 생기는문제

- 위에서 처럼 {}객체를 만들고 function을 넣는방식은 문제가없다.
  - js는 일급함수여서 함수를 넘기고 받고 사용하는게 가능하다.
- 그런데 해당 함수안에 state를 사용하게 되면 생기는 문제가 있다.

- 아까 action함수를 보자

```js

let action = (atk)=>{
  let intext = textDiv.innerText
  //타이머 중인지 확인
  if(isNaN(intext)){
      //체력이 없음
      return [false,time2sec(intext)]
  }

  let left = Number(textDiv.innerText)
  if(atk===-1){
      return [true,left]
  }
  
  ...
  //hp를 atk만큼 줄이는 로직
}

```

- 일부 코드로 useState로 state로 선언해 보자

```js

const [hp,setHp] = useState(2);
const [time,setTime] = useState(100);

let action = (atk)=>{
  if(hp===0){
      //체력이 없음
      return [false,time2sec(time)]
  }

  if(atk===-1){
      return [true,hp]
  }
  
  ...

  //hp를 atk만큼 줄이는 로직
}

//hp를 출력
return <div>{hp}</hp>

```

- 이런식으로 변경이된다. 이게 잘 작동할까?
- action함수를 실행하면 hp와 time을 return해야 한다.
  - 하지만! `몇번을 실행해도 hp와 time은 초기값만 return`한다.
  - action은 atk을 변수로 받아서 hp를 줄였다고 하더라도 hp는 언제나 초기값을 리턴한다.
  - 비동기 문제가 아니고 action함수를 몇번을 실행해도 return되는 hp와 time은 초기값이다.
- 하지만 실제로 hp는 줄어들고있다는점이다!
  - 저 action내에서 console로 찍으면 hp는 여전히 초기값이다.
  - 하지만 해당hp를 컴포넌트에서 확인하면 hp는 줄어들고 있다는점이 더욱 미치게 했다.

### 정리

- 이런 구조를 만든 이유
  - 기존 js에서 box들을 만든 이유는 어떤 위치에서든 특정 컴포넌트를 조작해야 했었다.
  - 함수를 아래에서 올리면서 이런 구조를 만들었다.
- 해결 - redux
  - 어느 컴포넌트에서든 state를 확인가능해야함
  - 어느 컴포넌트에서든 state를 조작해야함
  - 전역 state관리 라이브러리인 redux를 적용하기로 함

- redux로 중앙에서 관리

```js
//하브렐 관련 reducer 정보

const initialState = {
  hp: {0:14,12:3,1:3,11:3,3:3,9:3,5:3,7:3,6:3},
  time: {0:100,12:100,1:100,11:100,3:100,9:100,5:100,7:100,6:100},
  timer: undefined,
  totalTime: 65,
  totalTimeFlag: false,
  yellowCount: 0,
  blueCount: 0,
}

...

//위에서의 action이라는 함수가 아래의 2줄로 해결이 되었다.
attack:(state,action)=>{
  const { index, value } = action.payload;
  state.hp[index] = Math.max(state.hp[index]-value,0);
},
//시간은 함수를 분리
countTime:(state,action)=>{
  const { index } = action.payload;
  state.time[index]-=1;
},

```

- action이라했던 함수는 역할이 많았다.
  - 해당 컴포넌트의 state라 하는 hp(체력), time(복구시간)을 확인
  - atk이라는 데미지만큼 hp에서 빼기
  - time을 -1씩 줄이기 등등
- attak이라는 함수는 hp라 할 수 있는 체력에서 빼기만 해준다.
- 이는 어느 컴포넌트에서도 state를 조작할 수 있게 된것이다.
- state를 조회하고 싶으면 useSelector를 통해 바로 접근이 되었다.
