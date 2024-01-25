---
layout: post
title: node-gyp나오는npm install에러
categories: 기타개발
tag: [node-gyp, npm, tfjs-node]
toc: true
---

# node-gyp가 wanrning나오면서 에러가 표시

- window를 기준으로 작성했습니다.

## node-gyp란?

- node-gyp is a cross-platform command-line tool written in Node.js for compiling native addon modules for Node.js.
  - cross-platform 도구로 nodejs가 js가 아닌 다른 언어로 된 라이브러리나 모듈을 사용할때 쉽게 설치해주는것

## 발생한 문제

### 어디서 발생했는지

- `tfjs-node`를 설치하는데 발생 하였다.
  - tfjs-node내에 c++ 모듈을 불러와야 하는데 이때 node-gyp에서 문제가 발생

### node-gyp설치해서 해결하기

- node-gyp가 없어서 발생하는 문제일수 있으니 아래 명령어를 실행하면 해결될 수 있다 한다.

```sh
npm install -g node-gyp
npm install --global windows-build-tools
```

### visual studio 오류

```sh
gyp ERR! find VS **************************************************************
gyp ERR! find VS You need to install the latest version of Visual Studio
gyp ERR! find VS including the "Desktop development with C++" workload.
gyp ERR! find VS For more information consult the documentation at:
gyp ERR! find VS https://github.com/nodejs/node-gyp#on-windows
gyp ERR! find VS **************************************************************
```

- 저는 위 같은 에러가 발생
- 읽어보면 visual studio가 없다, 설치하라는 표시가 나온다.
- 공식문서를 보니 `python`과 `visual studio build tool`이 별도로 설치해야 하는듯 하다.
  - [https://www.npmjs.com/package/node-gyp#on-windows](https://www.npmjs.com/package/node-gyp#on-windows)
- python설치
  - 버전은 따로 명시 없으며, 설치시 최신 버전으로 설치
  - [python설치](https://apps.microsoft.com/search/publisher?name=Python%20Software%20Foundation&publisher=Python%20Software%20Foundation&hl=en-us&gl=US)
    - node-gyp에서는 ms store로 소개하지만, 각자 알아서 python을 설치
- visual studio build tool 설치
  - C++를 사용한 데스크톱 개발(Desktop development with C++)만 선택하여 설치해줍니다.

### tfjs-node의 버전 문제 확인

- tfjs-node 설치하는데 에러가 계속 발생

```sh
npm ERR! code 1
npm ERR! path H:\naverIntern\w4_face\node_modules\@tensorflow\tfjs-node
npm ERR! command failed
npm ERR! command C:\Windows\system32\cmd.exe /d /s /c node scripts/install.js
npm ERR! CPU-windows-4.16.0.zip
npm ERR! https://storage.googleapis.com/tensorflow/libtensorflow/libtensorflow-cpu-windows-x86_64-2.9.1.zip
npm ERR! * Downloading libtensorflow
npm ERR!
npm ERR! * Building TensorFlow Node.js bindings
npm ERR! symlink ./lib/napi-v9 failed:  Error: Command failed: node scripts/deps-stage.js symlink
./lib/napi-v9
npm ERR!   * Symlink of lib\napi-v9\tensorflow.dll failed, creating a copy on disk.
npm ERR! node:internal/process/promises:288
npm ERR!             triggerUncaughtException(err, true /* fromPromise */);
npm ERR!             ^
npm ERR!
npm ERR! [Error: ENOENT: no such file or directory, copyfile 'H:\naverIntern\w4_face\node_modules\@tensorflow\tfjs-node\deps\lib\tensorflow.dll' -> 'H:\naverIntern\w4_face\node_modules\@tensorflow\tfjs-node\lib\napi-v9\tensorflow.dll'] {
npm ERR!   errno: -4058,
npm ERR!   code: 'ENOENT',
npm ERR!   syscall: 'copyfile',
npm ERR!   path: 'H:\\naverIntern\\w4_face\\node_modules\\@tensorflow\\tfjs-node\\deps\\lib\\tensorflow.dll',
npm ERR!   dest: 'H:\\naverIntern\\w4_face\\node_modules\\@tensorflow\\tfjs-node\\lib\\napi-v9\\tensorflow.dll'
npm ERR! }
npm ERR!
npm ERR! Node.js v18.17.1
npm ERR!
npm ERR!     at ChildProcess.exithandler (node:child_process:419:12)
npm ERR!     at ChildProcess.emit (node:events:514:28)
npm ERR!     at maybeClose (node:internal/child_process:1091:16)
npm ERR!     at ChildProcess._handle.onexit (node:internal/child_process:302:5) {
npm ERR!   killed: false,
npm ERR!   cmd: 'node scripts/deps-stage.js symlink ./lib/napi-v9'
npm ERR! }

npm ERR! A complete log of this run can be found in: C:\Users\sjb37\AppData\Local\npm-cache\_logs\2024-01-25T04_55_40_802Z-debug-0.log

```

- 위처럼 발생한 경우 node의 버전을 확인해야 한다.
  - tfjs-node가 4.16.0을 설치시, node는 19.9.0이상이어야 한다
    - [tf forum확인](https://discuss.tensorflow.org/t/tensorflow-js-examples-setup/21713)
- tfjs-node를 4.16.0을 깔려고 했으므로 node를 19.9.0으로 바꾸니 설치완료

## 참고

- [node-gyp공식](https://www.npmjs.com/package/node-gyp)
- [https://www.inflearn.com/questions/802996/tfjs-node 안깔려서](https://www.inflearn.com/questions/802996/tfjs-node-%EC%95%88%EA%B9%94%EB%A0%A4%EC%84%9C)
- [https://jellybeanz.medium.com/Windows 에서 npm install 시 node-gyp rebuild 동작시 Error 발생으로 npm install 이 되지 않는 경우 해결 방법](https://jellybeanz.medium.com/windows-%EC%97%90%EC%84%9C-npm-install-%EC%8B%9C-node-gyp-rebuild-%EB%8F%99%EC%9E%91%EC%8B%9C-error-%EB%B0%9C%EC%83%9D%EC%9C%BC%EB%A1%9C-npm-install-%EC%9D%B4-%EB%90%98%EC%A7%80-%EC%95%8A%EB%8A%94-%EA%B2%BD%EC%9A%B0-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95-64e2cb3d9d76)
- [https://velog.io/@wjs4199/React-npm-install시 node-gyp 에러](https://velog.io/@wjs4199/React-npm-install%EC%8B%9C-node-gyp-%EC%97%90%EB%9F%AC)
