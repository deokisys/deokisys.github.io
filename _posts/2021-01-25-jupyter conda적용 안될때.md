---
layout: post
title: jupyter conda적용 안될때
categories: python
tags: [jupyter,python, anaconda]
---

## 문제
- conda로 가상환경만들고, conda로 모듈을 설치하고 `jupyter notebook`을 하였지만 jupyter에서는 모듈이 import가 안됨
- terminal에서는 conda가상환경 적용도 되고 모듈도 설치되었는데 jupyter는 적용이 안되고 있었음

## 해결
- `conda intall notebook`를 해줘서 conda에서 jupyter가 돌게 해준다.

## 확인
- `jupyter kernelspec list`를 하면 conda가 깔린 폴더를 보여주는지 확인하면 된다.