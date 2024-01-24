---
layout: post
title: 순수js웹에 react적용기 - javascript에서 typescript 전환
categories: 웹
tags: [react, javascript, webpack, babel, typescript, typescript전환, declare, typescript svg]
toc: true
math: true
---

# javascript에서 typescript로 전환하기

- 이제는 javascript의 연습도 중요하지만 typescript의 연습도 하기 위해 전환
- webpack에서 javascript와 react도 설정해보고, 이제는 typescript로 바꾸는것도 연습한다.

## 설치

```
npm i -D @babel/preset-typescript
npm i -D @types/react-dom

```

### 방식

- 알아보니 두가지 방법이 존재한다.
- typescript가 자체적으로 바벨과 같은 효과를 가지고,

## 에러들

- 적용하면서 나온 에러를 정리해보았다.

### tsconfig.json 에러

```
    tsconfig.json' 구성 파일에서 입력을 찾을 수 없습니다. 지정된 '포함' 경로는 '["**/*"]'이고 '제외' 경로는 '[]'이었습니다
```

- 이 에러는 src아래의 파일들이 ts가 아니어서 생기는 에러
- ts로 전환하면 자연스럽게 없어졌다.

### svg파일을 import 못하는 에러

- js에서는 그냥 import하면 되지만 ts는 svg를 찾지 못하였다.

```js
import harbTitle from "../../assets/title2.svg";
```

- 이를 위해 ts에 해당 타입을 어떻게 부를지 설정하게 파일을 하나 만들어주면 된다.
  - 파일명은 `svg.d.ts`로 지어준다.
  - 파일명은 큰 상관은 없다. 관리하기 편하게 폴더나 파일명을 통일하면 된다.

```ts
declare module "*.svg" {
  const value: React.FunctionComponent<React.SVGAttributes<SVGElement>>;
  export default value;
}
```

- 만약 useState에 쓰려면 아래처럼 타입을 지정해서 사용하면 된다.

```ts
const [src, setSrc] = useState<React.FunctionComponent<React.SVGAttributes<SVGElement>> | null>(
  null
);
```

### svg를 img의 src로 사용하는 법2

- 위 처럼 가져오면 svgElement로 가져온다.
- 그러면 img src에 타입 문제가 발생한다.
  - 작동에는 문제가 없다! 이미지도 잘 가져오지만, warning이 나온다.
- 이걸 해결하기 위해 declare를 아래처럼 수정해주면 된다.

```ts
declare module "*.svg" {
  const content: any;
  export default content;
}
```

- 이렇게 가져오면 img src로 바로 쓰는데 타입 에러가 안뜨게 된다.

```ts
import imgsvg from "../aa.svg";

const [src, setSrc] = useState<string>("");

setSrc(imgsvg)

<img src={src}>
```

## 참고

- [webpack공식](https://webpack.kr/guides/typescript/)
- [webpack typescript](https://velog.io/@jungsangu/Webpack-Babel%EB%A1%9C-React-Typescript-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0)
- [tsconfig.json에러](https://bny9164.tistory.com/46)
- [TypeScript가 SVG 파일을 import하지 못하여 에러가 발생할 때](https://tesseractjh.tistory.com/227)
- [TS에서 svg import 안될 때](https://velog.io/@xmun74/TS%EC%97%90%EC%84%9C-svg-import-%EC%95%88%EB%90%A0-%EB%95%8C)
