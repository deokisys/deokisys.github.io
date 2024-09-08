---
layout: post
title: dependencies와 devDependencies peerDependencies
categories: 공부
tag: [dependencies, devDependencies, peerDependencies]
toc: true
---

# dependencies와 devDependencies peerDependencies

- 이것들은 node.js의 어플리케이션에서 필요한 패키지들을 정의하기 위해 사용된다.
- package.json에 사용되는 필드이다.
- 패키지 관리와 환경을 조정하는데 효율적으로 관리할 수 잇다.

## dependencies

- node.js 어플리케이션이`런타임`에 필요한 패키지들을 정의한 package.json의 필드

- `npm install <패키지명>`으로 설치한다.
  - `-P` or `--save-prod` 옵션을 쓰면 되지만, 생략해도 된다.
- 대표적으로 `express`나 `react`와 같은 실행할때 필수로 사용하는 패키지들이다.

## devDepencies

- 어플리케이션이 `개발 및 빌드 과정`에서 필요한 패키지들을 정의한 필드
- 개발중에 필요한 도구들을 테스트, 빌드, 린터가 포함된다고 한다.
- `npm install -D <패키지명>`으로 설치한다.
  - `-D` or `--save-dev` 옵션을 쓰면 된다.
- 대표적으로 테스트도구 `jest`같은게 포한된다.

### dependencies와 devDepencies둘을 나눈 이유는?

- 해당 패키지를 실제 어플에 필요하면 dependencies에 포함하고, 개발할때만 사용하면 devDepencencies에 포함하면 된다.
- 둘을 분리하여 최소한의 패키지만들 어플리케이션에 포함시켜서 용량을 최소화 하고, 보안을 높이려는 목표이다.
- 왜 보안일까
  - 만약 개발할때만 사용한 패키지 하나가 보안에 취약하다고 해보자.
  - 실제 어플리케이션엔 영향도 주지않지만 용량도 차지하고, 해당 패키지를 통해 서비스에 접근할 수 잇는 문제가 생긴다.
  - 실행하는데 최소한의 패키지를 사용하므로서 취약점을 줄이는 것이다.

## peerDependencies

- 이는 실제 어플에서 사용하는 패키지가 아니지만, 호환성을 위해 표기하는 패키지들을 정의하는 필드

  - `In some cases, you want to express the compatibility of your package with a host tool or library, while not necessarily doing a require of this host.`

- 어쩔때 사용되나?

  - 만약 A가 r.1을 의존해서 사용하고 있다고하자.
  - 근데 B가 A를 의존하고, B는 r.2를 사용한다고 하자.
    - 그러면 A의 r과 B의 r이 호환성 문제가 될 수 도 있다.
  - 그럴때 A의 peerDependencies에 r.1을 명시, B의 peerDependencies에 r.2를 명시하는것이다.
  - 이러면 A의 r.1과 B의 r.2가 서로 충돌을 피하고 호환성에 명시를 할 수 있다고 한다.

- 위의 예시 package.json

```json

// A의 package.json
{
  "name": "A",
  "version": "1.0.0",
  "peerDependencies": {
    "r": "1.0.0" //A는 r.1을 의존중
  }
}

// B의 package.json
{
  "name": "B",
  "version": "1.0.0",
  "dependencies": {
    "A": "^1.0.0", //위의 A를 의존
    "r": "2.0.0" //B에서는 r.2를 사용한다.
  }
}

```

- npm3~6까지는 자동으로 설치되지 않고, 경고문구만 뜬다.
- npm7부터는 기본 설치되고, 버전이 안맞으면 에러도 발생한다.

## 참고

- [npmDocs package](https://docs.npmjs.com/cli/v10/configuring-npm/package-json)
- [NPM: dependencies vs. devDependencies vs. peerDependencies](https://medium.com/@DevChris01/npm-dependencies-vs-devdependencies-vs-peerdependencies-82760bd630d)
- [nodejs Peer Dependencies](https://nodejs.org/en/blog/npm/peer-dependencies/)
