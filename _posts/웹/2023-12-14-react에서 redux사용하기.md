---
layout: post
title: react에서 redux사용하기
categories: 웹
tags: [react, redux]
toc: true
math: true
---


# 리덕스
- 상태관리를 좀더 편하게 하기 위해 사용한다.
- 전역에서 상태를 관리해서 드릴처럼 뚫어서 자식에게 전달하거나, 자식에서 위로 올라가야 하는 경우를 좀더 간결하게 해준다.

## 용어
- action
    - 상태를 업데이트(변경)하는 함수
- 리듀서
    - 상태를 어떻게 변경할지 결정하는 함수

## 설치

- 리덕스에서 좀더 편하게 쓰라고 toolkit으로 만들었다고 한다.
    - `Redux Toolkit은 Redux 로직을 작성하기 위해 저희가 공식적으로 추천하는 방법입니다`
    - RTK라고도 부르는듯
`npm install @reduxjs/toolkit react-redux`


### 빠른 사용 - 설정

- 루트 컴포넌트

```js
import store from './reducer'
import { Provider } from 'react-redux'

//Provider로 감싸준다.
ReactDom.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
);

```

- reducer.js
    - 여러 reducer들을 관리할수있따.

```js
import { configureStore } from '@reduxjs/toolkit'
import harbrelReducer from './harbrel'//특정 reducer를 정의

export default configureStore({
  reducer: {
    harbrel:harbrelReducer,//이런식으로 모듈처럼 분리해서 관리한다.
  }
})
```

- 그러면 reducer를 정의

```js
import { createSlice } from '@reduxjs/toolkit'

//state를 선언
const initialState = {
  value:0,
  arr:[1,1],
}

const harbrelSlice = createSlice({
  name: 'harbrel',//이름
  initialState,//state
  reducers: {
    //state를 조작하는 action을 정의
    //action명:(state,action)=>{state.state명으로 접근, action.payload로 접근}

    //dispatch(actionName({value:10}));
    actionName: (state,action)=>{
        //action에는 payload에 들어가서 사용된다.
        const {value} = action.payload;
        state.value = value;
    },
    //dispatch(add());
    add: (state)=>{
        state.value +=1;
    },
    //dispatch(arrAdd({index:1, value:12}));
    arrAdd: (state,action)=>{
        const {index, value} = action.payload;
        state.value[index] = value;
    },
    //dispatch(addN(10));
    addN (state,action)=>{
        //바로 사용도 가능
        state.value +=action.payload;
    },
  }
})

//여기에 만든 action들을 export한다.
export const { actionName,add,arrAdd} = harbrelSlice.actions
export default harbrelSlice.reducer

```

### 빠른 사용 - 사용하기

- 컴포넌트에서 바로 사용하는법
    - useSelector로 state를 가져온다.
    - useDispatch로 action을 수행한다.

```js

import { useSelector, useDispatch } from 'react-redux'
//위에서 정의한 reducer에서 action을 import한다.
import { actionName,addN } from "../../../reducer/harbrel";

//state가져오기
const reduxValue = useSelector((state) => state.harbrel.value);

//action쓸 준비
const dispatch = useDispatch()


//action작동
dispatch(actionName({value:100}));
//이 구조를 잘 맞춥시다.

dispatch(addN(12));
//payload를 직접적으로 사용하는경우 바로 값을 넣어도 된다. 

```




## 참고
- [redux공식](https://ko.redux.js.org/introduction/getting-started/)
- [redux tookit공식](https://redux-toolkit.js.org/tutorials/quick-start)