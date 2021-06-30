---
layout: post
title: flask_cors오류
categories: 웹
tags: [웹,flask,cors]
toc : true
math : true
---

- [출처](https://webisfree.com/2020-01-01/python-flask%EC%97%90%EC%84%9C-cors-cross-origin-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)

### 나오는이유
- 간단하게 주소가 서로 다르므로 보안의 문제로 나오는 경고이자 에러

### 라이브러리
- `pip install flask-cors`


### 적용법
- import 
  - `from flask_cors import CORS, cross_origin`
- 사용
```python
  ...
  app = Flask(__name__)
  CORS(app,resources={r'*': {'origins': '*'}})# app을 CORS로 감싼다.
  ...
```

### 기타 설명
- 모든 리소스에 대해 접근
  - `CORS(app,resources={r'*': {'origins': '*'}})`
