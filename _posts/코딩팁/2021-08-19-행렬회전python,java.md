---
layout: post
title: 행렬회전python,java
categories: 코딩테스트
tags: [코딩테스트, java, python,행렬회전,rotate]
toc : true
math : true
---

## 행렬회전
- 매일 구현하려고 하면 햇갈려서 저장용

## 간단 설명
- 새로운 행렬을 만들어주고 원본 배열을 돌려서 저장하는방식
  - 새로 만들어지는 배열(90도,-90도)은 가로와 세로길이가 서로 뒤바뀐다.
- 90도 회전은 원본 [x,y]가 회전후에는 [y,원본세로길이-x]가 되는 규칙이 존재
- 180도 회전은 원본 [x,y]가 회전후에는 [원본세로길이-x,원본가로길이-y]되는 규칙이 존재
- 270도(-90도)회전은 원본 [x,y]가 회전후 [원본가로길이-y,x]되는 규칙이 존재

## 코드
- 파이썬

```python
def rotate90(g): # 90도
    # 0,0 -> 0,5
    # 0,1 -> 1,5
    # 0,2 -> 2,5
    # 0,3 -> 3,5
    # 0,4 -> 4,5
    # 0,5 -> 5,5
    # x,y = y,끝-1-x
    # 5,0 -> 0,0
    tmp = [[0]*len(g) for _ in g[0]]
    
    for i in range(0,len(g)):
        for j in range(0,len(g[0])):
            tmp[j][len(g)-1-i] = g[i][j]
            
    return tmp
    
def rotate180(g): # 180도
    # 0,0 -> 5,5
    # 0,1 -> 5,4
    
    # 1,0 -> 4,5
    tmp = [[0]*len(g[0]) for _ in g]

    for i in range(0,len(g)):
        for j in range(0,len(g[0])):
            tmp[len(g)-1-i][len(g[0])-1-j] = g[i][j]
    
    
    return tmp


```

- 자바 

```java

//우측 90도시계방향
public static int[][] turn90(int[][] map) {
    //오른쪽 90도, 시계방향
    int H = map.length;
    int W = map[0].length;
    int[][] nMap = new int[W][H];
    
    //이런 형태도 가능
    // for (int i = 0; i < W; i++) {
    // 	for (int j = 0; j < H; j++) {
    // 		nMap[i][j] = map[H-1-j][i];
    // 	}
    // }

    for (int i = 0; i < H; i++) {
        for (int j = 0; j < W; j++) {
            nMap[j][H-1-i] = map[i][j];
        }
    }
    return nMap;
}

//좌측90도 반시계
public static int[][] turn270(int[][] map) {
		
    //왼쪽 90도, 시계방향
    int H = map.length;
    int W = map[0].length;
    int[][] nMap = new int[W][H];
    //이런 형태도 가능
    // for (int i = 0; i < W; i++) {
    // 	for (int j = 0; j < H; j++) {
    // 		nMap[i][j] = map[j][W-1-i];
    // 	}
    // }

    for (int i = 0; i < H; i++) {
        for (int j = 0; j < W; j++) {
            nMap[W-1-j][i] = map[i][j];
        }
    }
    
    return nMap;
}
```