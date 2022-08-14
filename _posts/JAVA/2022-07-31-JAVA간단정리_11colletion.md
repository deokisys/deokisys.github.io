---
layout: post
title: JAVA간단정리_11collection
categories: JAVA
tags: [JAVA]
toc: true
---

# collection
- list계열
  - list, set
- map계열
  - map 

- add
- contains, equals, isEmpty, size
- clear
- toArray


## list
- list(arraylist,linkedlist, stack), queue
- 순서가 존재
- 중복 허락
- get, indexof, remove(index), set(index, e)수정
- 조회 빠름
- 새로운 배열을 만드는 형식
- 비순차 추가, 삭제 오래걸림
  - 순차는 빠름
- 삭제시 list크기 바뀌는거 조심

### linked list
- 순차 추가삭제 느림
  - 비순차는 빠름
- 조회 느림

## set
- 순서 없음
- 중복 없음
- 수정 없음
  - 지우고 추가하면 되니까
- 동일 기준
  - equeals, hashcode가 동일


## map
- 순서 없음
- 키 중복 없음
- key, value
- put
  - putIfAbsent - 없으면 추가하기
- containsKey, containsValue, keySet,get(키),vlaues,size,isEmpty
- clear, remove(키)
- put- 수정도 가능


## 정렬
- Collections.sort(콜랙션 객체)
  - 값기준, map은 키기준

### comparator
- 정렬하는 조건으로 직접 사용한다.
- `new comparator<비교할 객체>(){compare(비교객체 1, 비교객체 2){}//를 오버라이드}`
- comparator가 덮어서 사용이 가능

### 람다식
- 오버라이드 되는 메소드의 인자 -> 오버라이드 되는 메소드의 구현부

### comparable
- 해당객체에 직접 적용
- ~와 비교한다는 뜻
- compareTo를 작성해야한다.
- 객체 implements comparable<비교객체>{}
  - compareTo(비교할객체 o){ return this.number.compareTo(o.number)}