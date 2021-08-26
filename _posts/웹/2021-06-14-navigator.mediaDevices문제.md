---
layout: post
title: navigator.mediaDevices문제
categories: 웹
tags: [웹,http,https,navigator.mediaDevices]
toc : true
math : true
---

## navigator.mediaDevices가 undefined되는 이유
- 웹캠을 적용하려는데 위의 코드를 통해 웹캠의 정보를 가져오는게 불가능한 경우
- https가 아닌 http인 경우 보안의 이슈로 불러오지 못한다.
- 해결 방법

### http를 https로 변경해주기
- openssl를 통해 간단한 데모사이트를 https로 바꿔서 진행한다.
- 크롬에서 openssl적용하기를 통해 진행해보기
- [참고1](http://mcchae.egloos.com/v/11143246)
- [참고1](https://www.lesstif.com/system-admin/openssl-root-ca-ssl-6979614.html)


### 귀찮다 그냥 확인만 하고싶다!
- 크롬의 보안을 직접 해제하여 특정 주소에서는 보안요소를 무시하도록 하면된다.
- `chrome://flags/#unsafely-treat-insecure-origin-as-secure` 로 이동
- 주소 및 포트번호를 적고 enabled로 적용한다.
- [출처](https://velog.io/@juna-dev/navigator.mediaDevices-undefined-%ED%95%B4%EA%B2%B0)