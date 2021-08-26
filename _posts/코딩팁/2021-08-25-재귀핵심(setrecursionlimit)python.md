---
layout: post
title: 재귀핵심(setrecursionlimit)python
categories: 코딩테스트
tags: [코딩테스트, python,setrecursionlimit,재귀핵심]
toc : true
math : true
---

## 재귀핵심(setrecursionlimit)(python기준)
- 재귀와 같은 여러번 함수를 호출하는 경우 그 한계를 돌파하게 해줘서 어느순간 사라지는 호출을 막아준다.
- 재귀할때는 일단 쓰고 시작해야한다.

## 코드
```python
import sys
sys.setrecursionlimit(300000)
```

## 사용된곳(더있을 수 있음)
- https://programmers.co.kr/learn/courses/30/lessons/76503
- https://programmers.co.kr/learn/courses/30/lessons/64063