---
layout: post
title: AI 간단 용어 정리2 cost function
categories: AI 
tags: [ai, linear, cost function, loss function, gradient descent]
toc: true
math: true
---

## cost function(loss function) 이란
- 가설과 실제 데이터가 얼마나 다른지 측정하는 함수

- 가설 함수 H
   - 일반적인 linear는 $$ H(x)=Wx+b $$
   - weight(W)와 b로 함수가 결정된다.

- 데이터값(y)와 가설 함수의 값(Hx)의 차이들을 계산하는 식을 cost function 으로 사용한다.

- 최대한 cost function의 값을 작게 하는게 학습의 목적이 된다.
   - 최소화 하는 W와 b의 값을 구해야 한다.

## cost function 최소화 방법
- cost function의 최소를 찾아야 한다.
   - 이차 함수인경우 곡선의 형태를 보여준다.
   - 곡선의 제일 최 하단의 값을 구해야 한다.


### gradient descent 알고리즘을 사용한다.
- 경사를 따라 내려가는 알고리즘(경사하강법)
- 경사가 완만해지는, 기울기의 절대값이 낮아지는 방향으로 cost function의 W,b의 값을 조정한다.
- $$W := W-\alpha\frac{\delta}{W\delta}cost(W)$$
   - 기존 W에서 learning rate라고 하는 alpha와 cost(W)를 미분한값을 곱한것을 빼준다는 의미
   - "W-a*기울기"가 되므로 기울기가 +이면 아래로 볼록한 그래프의 오른쪽에 있다는 의미가 되며, 왼쪽으로 이동하도록 learing rate * 기울기 만큼 왼쪽으로 이동하게 되는 공식이다.
   - 최소보다 왼쪽이면 기울기가 -가 되므로 +만큼 오른쪽으로 이동하게 된다.

### convex function
- 어느곳에서 시작해도 최소의 값이 같은 함수



출처:https://youtu.be/TxIVr-nk1so