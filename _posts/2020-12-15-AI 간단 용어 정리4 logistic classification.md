---
layout: post
title: AI 간단 용어 정리4 logistic classification
categories: AI 
tags: [ai, classification, sigmoid]
toc: true
math: true
---
## classification
- 분류하는것

### 예시들
- binary처럼 0,1로 두가지로 분류하는 간단한 classification이 있다.
- 스팸
   - 스팸이면1, 스팸이 아니면 0
- 페이스북
   - 피드에 보일거면1, 안보일거면0
- 주식
   - 오를지, 안오를지
- 시험
   - 합격1, 불합격0

### linear로 적용
- H(x) = Wx+b이면, 0보다 작거나 1보다 더 큰값으로 나올수가 았다.
  - W가 0.5이고 b가 0일때 x=[1,2,3,5]인 데이터에 맞게 학습했을때, x=10인 데이터를 넣었을때 1보다 큰 값이 나오게 된다.


## logistic classificaiont
- 0과 1로 출력값을 압축 하여 분류

### sigmoid
![sigmoid](https://upload.wikimedia.org/wikipedia/commons/thumb/8/88/Logistic-curve.svg/1024px-Logistic-curve.svg.png)
- 0과 1사이에 s와 같은 곡선의 형태로 곡선의 형태로 표현된다.
- $$ g(z) = \frac{1}{(1+e^{-z})}$$
   - 이 공식은 큰값들은 1에 가까워지며, 작은값들은 0에 수렴한다.

### logistic hypothesis
- sigmoid의 입력되는 z=WX가 된다.
- 가설 함수 H(X) = g(z)가 되어서 정리한 공식은 아래가 된다.
- $$ H(X) = \frac{1}{1+e^{-W^{T}X}}$$


출처:https://youtu.be/PIjno6paszY