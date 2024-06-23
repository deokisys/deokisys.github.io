---
layout: post
title: git의 rebase
categories: 공부
tag: [git, rebase]
1toc: true
---

# rebase란?

- git에서 두개의 브랜치를 합치는데에 두가지 방법중 하나
  - Merge와 Rebase 이렇게 두가지 존재한다.
- 하지만 대부분 Merge를 자주 사용하고, Rebase는 위험하다고 자주 듣는다.
- 왜 위험한지와 어떻게 작동하는지 정리해본다.

## 작동방식

- a라는 브랜치를 기준으로 b1, b2로 나뉜 두 브랜치가 존재할때
- b2에서 rebase b1을 진행한다면?
  - b1과 b2의 공통부모인 a를 확인
  - a부터 현재 b2까지의 변경내역(diff)를 임시로 저장
  - b1으로 이동하여 저장된 변경내역들을 차례로 적용한다.
  - 그럼 `a-b1-b2'`와 같이 1줄로 만들어지게 된다.
- 결국 rebase란 현재까지의 변경사항들을 목표로 하는 브랜치의 히스토리로 적용하는 것이다.
- 위와 같은 a,b1,b2의 상황에서 merge를 한다면 같은 결과가 만들어지지만 무엇이 다를까

## merge와 차이점

- 브랜치의 log, 히스토리 내역의 차이가 있다.
- merge는 두 브랜치가 하나로 합쳐지는 형태로 보여진다.
- rebase는 선형으로 만들어져 좀더 깔끔한 형태를 보여준다.
- 이런 이유로 커밋을 깔끔하게 적용하고 싶을때 사용한다고 한다.

## 옵션

### `--onto`옵션

- `git rebase --onto <newbase> <upstream> <branch>`

  - newbase는 이동할 브랜치
  - upstream은 제외할 브랜치
  - branch는 재배치할 브랜치
  - 사용시 재배치할 브랜치로 checkout한다음 사용하면 된다.

- 특정 브랜치의 변경내역을 제외하고 rebase할때 사용한다.
  - 만약 a브랜치로 부터 main, dev로 나뉘어 있고 dev에서 serv, cli로 나뉘어있다고 가정
  - 이럴때 main에 serv가 관여한것을 제외하고 cli브랜치에서의 변경사항만 main에 합치는게 가능
- 이렇게 하면 오로지 cli브랜치에서 작업한 내용만 main으로 적용이 가능하다.
- 브랜치 변경사항을 유연하게 재배치하고 원하는 커밋위에 다시 적용이 가능

### interative 옵션

- `-i`로 rebase를 수행할때 대화형으로 작업하는 기능이다.
- 해당 옵션을 사용하면 아래의 명령어를 활용해 작업이 가능하다.
  - pick: 커밋을 그대로 둡니다.
  - reword: 커밋 메시지를 수정합니다.
  - edit: 커밋을 수정하고 커밋 내용을 변경합니다.
  - squash: 커밋을 이전 커밋과 합칩니다.
  - fixup: 커밋을 이전 커밋과 합치고, 현재 커밋 메시지를 무시합니다.
  - drop: 커밋을 삭제합니다.
- 이를 통해 특정 커밋을 수정, 편집이 가능하다.

## 위험성

- 이미 공개 저장소에 push 한 커밋을 rebase하지 않기
  - rebase는 기존 커밋을 그대로 사용이 아닌, 다른 커밋을 새로 만든다.
- rebase는 로컬에서 작업할때 히스토리 정리할때 사용해야 한다.

## 참고

- [git연습 해보기](https://learngitbranching.js.org/?locale=ko)
  - 연습문제에서 고급 문제에서 rebase를 연습해 볼 수 있다.
- [3.6 git브랜치 rebase](https://git-scm.com/book/ko/v2/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-Rebase-%ED%95%98%EA%B8%B0)
