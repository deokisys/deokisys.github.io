---
layout: post
title: Axios x-www-form-urlencoded로 전송하기
categories: 웹
tags: [axios,x-www-form-urlencoded]
toc : true
math : true
---

## x-www-form-urlencoded 전송법
- 코드 스크랩 저장용

### 코드

```js

//전달하는 파라미터 지정
const params = new URLSearchParams()
params.append('키', '값')
params.append('키', true) //bool타입

//주소와 header 지정
const url = "url"
const config = {
    headers: {
        'Content-Type': 'application/x-www-form-urlencoded'//header타입 
    }
}

//전송
axios.post(url, params, config)
.then((result) => {//정상 응답
    res.json({"status":"good"})
})
.catch((err) => {//에러
    console.log(err)
    res.json({"status":"error"})
})
```


### 출처
- https://gist.github.com/akexorcist/ea93ee47d39cf94e77802bc39c46589b