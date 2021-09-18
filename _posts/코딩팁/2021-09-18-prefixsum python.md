---
layout: post
title: prefixsum python
categories: 코딩테스트
tags: [코딩테스트, python,구간합,dp,다이나믹,팁]
toc : true
math : true
---

## prefixsum
- 구간합에서 빠르게 계산이 가능

## 간단 설명
- 배열n이 있을때 0~i까지의 합 or i~j까지의 합을 구하라는 문제를 이중 for문이 아닌 한번의 for문으로 해결
- 각 배열n의index마다 0~index까지의 합을 미리 저장하면, a~b까지의 구간합은 n[b]-b[a]로 바로 계산이 가능
- 합을구할때는 sum[i] = sum[i-1] + n[i]를 통해 한번의 반복문으로 각 배열마다 합을 저장 가능하다.
- 유명하기도하고 dp로도 생각되는 문제라고 한다.

## 코드
```python

n = [-1,32,1,423,12,4,-3,23,123,1]# 원본배열
sumN = [] # 합이 저장되는 배열

for i,e in enumerate(n):
    if i==0:
        sumN.append(e)
    else:
        sumN.append(sumN[i-1]+n[i])

```