---
layout: post
title: 짜잘 ncloud sudo팁
categories: 기타개발
tags: [sudo,npm]
toc : true
math : true
---


## ncloud의 sudo 오류
- `sudo: unable to resolve host sjb`
- 에러는 아니지만 나오는 경고창
  - `/etc/hosts`의 127.0.0.1에 localhost라 적힌것을 `/etc/hostname`의 사용자 이름으로 바꿔준다.
  - 둘의 이름이 달라서 생긴 오류라고 한다.

### npm 관리자권한 오류
- npm으로 -g옵션을 통해 설치를 하는데 permission denied에러가 나는경우
  - nvm으로 관리를 하는경우 아래의 명령어로 권한을 부여해준다.
  - `sudo chown -R $(whoami) ~/.nvm`

## 참고
- https://2ssue.github.io/programming/ubuntu_resolve_host_error/
- https://stackoverflow.com/questions/51923277/npm-install-permission-denied-error-using-root-user