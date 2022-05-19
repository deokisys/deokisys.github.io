---
layout: post
title: 우선순위큐 PriorityQueue python
categories: 코딩테스트
tags: [코딩테스트, python,우선순위큐,PriorityQueue,팁]
toc : true
math : true
---

## 우선순위큐
- 큐를 우선순위를 주어 제일 우선순위가 높은 순 으로 꺼낼 수 있다.
- 파이썬에서 라이브러리로 사용가능

## 간단 설명

### 선언
- import
  - `from queue import PriorityQueue`
- 사용
  - `que = PriorityQueue()`

### 기본 사용
- 큐에 삽입시 우선순위를 입력해주면 내부적으로 알아서 정렬해준다.
- 입력
  - `que.put((우선순위,입력값))`
  - 우선순위는 기본 오름차순으로 부여된다.
- 꺼내기
  - `que.get()`
  - 꺼내는 순간 내부에서는 요소가 삭제된다.
  - (우선순위,값)으로 출력 되기 때문에 원하는 값만 꺼내기 위해서 `que.get()[1]`로 바로 확인하기도 한다.
- 길이 확인
  - `que.qsize()`
- 비어 있는지
  - `que.empty()`
  - 비어있으면 true, 아니면 false

### 알아야 할것
- que내부에 dict타입이 존재하면 오류가 발생한다
  - `TypeError: '<' not supported between instances of 'dict' and 'dict'`
  - dict타입 자체를 입력하면 오류가 나는것으로 보임
  - [https://github.com/aio-libs/janus/issues/199](https://github.com/aio-libs/janus/issues/199)

### 시간 복잡도
- heap모듈을 사용하여 O(log n)정도를 가진다고 한다.

## 출처
- https://www.daleseo.com/python-priority-queue/