---
layout: post
title: 행렬BFSpython
categories: 코딩테스트
tags: [코딩테스트, python,행렬BFS,bfs]
toc : true
math : true
---

## 행렬BFS(python기준)
- 매일 구현하려고 하면 햇갈리고 연습한내용 저장용

## 간단 설명
- 2차원 행렬이 있을때(g) i,j부터 시작하여 상하좌우로 연결된fg들을 찾는것
- 찾은 fg는 bg로 만들어서 중복으로 찾는것을 막는다.


## 코드
```python

def goBFS(g,i,j,fg,bg):
    #g에서 i,j부터 시작, fg는 찾을것, bg는 배경
    
    que = [[i,j]]
    blocksave = []
    while True:
        if len(que)==0:
            break
        pos = que.pop(0)
        if g[pos[0]][pos[1]] != fg:
            continue
        blocksave.append([pos[0],pos[1]])# 저장
        g[pos[0]][pos[1]] = bg
        
        #상
        if pos[0]>0 and g[pos[0]-1][pos[1]]==fg:
            # print("up")
            que.append([pos[0]-1,pos[1]])
        #하
        if pos[0]<len(g)-1 and g[pos[0]+1][pos[1]]==fg:
            # print("down")
            que.append([pos[0]+1,pos[1]])
        #좌
        if pos[1]>0 and g[pos[0]][pos[1]-1]==fg:
            # print("left")
            que.append([pos[0],pos[1]-1])
        #우
        if pos[1]<len(g)-1 and g[pos[0]][pos[1]+1]==fg:
            # print("right")
            que.append([pos[0],pos[1]+1])
            
    return blocksave
        
```

## 사용된곳
- https://programmers.co.kr/learn/courses/30/lessons/84021