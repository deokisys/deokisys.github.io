---
layout: post
title: 행렬간단DFSpython
categories: 코딩테스트
tags: [코딩테스트, python,행렬DFS,dfs,재귀]
toc : true
math : true
---

## 행렬DFS(python기준)
- 매일 구현하려고 하면 햇갈리고 연습한내용 저장용

## 간단 설명
- 인접리스트가 필요
  - adj[0]=[1,2,3] # 0과 연결된 정점정보를 가지는 리스트
- 인접리스트를 이용해 특정 정점에서 (기준은 그냥 인접리스트순서) 다음 위치로 가는 정점을 쭉가서 모든정점을 방문하는 간단한  dfs이다.
- 순서저장 없음, 우선순위 없음
- 모든 정점 방문만 한다.


## 코드
```python
def goDFS(a,cur,graph,visited):
    # 정점의정보, 현재위치, 인접리스트, 방문여부
    visited[cur] = 1# 방문!
    result = a[cur] #현재 정점 정보
    for n in graph[cur]:
        if visited[n]==0:
            result+ = goDFS(a,n,graph,visited) #현재정점 정보 + 자식의 정보
    return result # 현재정점부터 모든 자식의 정보를 더한값을 return한다.

# 인접리스트 만들기
def makeAdj(edges,a): # 간선정보, 정점크기
    graph = [[] for _ in a]
    for e in edges:
        graph[e[0]].append(e[1])
        graph[e[1]].append(e[0])
    return graph
```

## 사용된곳
- https://programmers.co.kr/learn/courses/30/lessons/76503