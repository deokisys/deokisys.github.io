---
layout: post
title: commit전에 prettier적용하기
categories: 웹
tag: [웹,프리티어, prettier, husky, lint-staged, commit전 prettier]
toc: true
---

# 프리티어를 커밋전에 적용하기

- husky 및 lint-staged를 활용하기

## husky와 lint-staged

- husky는 git에 훅을 걸어주는 라이브러리
- lint-staged는 commit된것에 대해 작업을 진행할 수있게 해주는 라이브러리

## 설치 

1. Husky와 lint-staged 설치

```bash
npm install --save-dev husky lint-staged
```

2. Husky 설정

```bash
npx husky install
```

3. Pre-commit Hook 설정

.husky/pre-commit을 생성해줍니다.

```bash
#!/usr/bin/env sh
. "$(dirname "$0")/h"

npx lint-staged //이걸 추가하면 됨
```

- 이는 commit하기 전에 해당 명령어를 작동한다는 의미


4. lint-staged 설정
package.json 파일에 lint-staged 설정을 추가

```json
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": "prettier --write"
  }
}
```
-  .js, .jsx, .ts, .tsx 파일이 커밋 전에 Prettier가 작동

### 커밋전인데 문제가 있다면?

- 만약 문제가 발생시 commit이 멈추게 되고, prettier가 적용된 파일을 다시 add해주고 commit을 진행하면 된다.




