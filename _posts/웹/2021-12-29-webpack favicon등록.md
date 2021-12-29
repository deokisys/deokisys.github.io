---
layout: post
title: webpack favicon 등록
categories: 웹
tags: [webpack,favicon,favicons-webpack-plugin]
toc : true
math : true
---

## webpack에서 favicon등록


### htmlwebpackplugin에 추가하기
- plugin으로 htmlwebpackplugin을 사용중이면 favicon의 경로를 넣어주면된다.

```js
plugins: [
  new HtmlWebpackPlugin({
    template: 'public/index.html',
    favicon: 'src/assets/images/logo.png',
  }),
]
```

### favicon plugin사용
- manifast나 그런 설정이 가능하고 좀더 favicon을 다양한 환경에서 보이도록 하고 싶을때 사용
- `npm install --save-dev favicons favicons-webpack-plugin`
  - favicons plugin설치
- webpack에 추가 

new FaviconsWebpackPlugin({
            logo: './public/favicon.ico',
        }),

## 참고
- https://yujo11.github.io/webpack/webpack%20favicon,%20manifest.json%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0/