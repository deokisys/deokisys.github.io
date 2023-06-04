---
layout: post
title: nextjs의 기본 route설정
categories: 웹
tags: [nextjs, 설정, router]
toc: true
math: true
---

# nextjs에서 기본 router를 설정하자.
`example.com/`으로접속시 `example.com/main`로 접속하게 하고 싶을때

## next.config.js를 수정하자.
- redirects를 활용하자.
- 코드를 확인

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  //redirects를 적용
  async redirects() {
      return [
        {
          source: '/',
          destination: '/main',
          permanent: true, //캐쉬여부로 true를 하면 캐쉬에서 저장해서 리다이랙트 진행
        },
      ]
    },
}
module.exports = nextConfig

```


## 참고
- [참고 stackoverflow](https://stackoverflow.com/questions/70318668/can-you-create-a-default-route-in-next-js)