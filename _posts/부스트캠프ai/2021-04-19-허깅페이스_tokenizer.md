---
layout: post
title: 허깅페이스_tokenizer
categories: AI 
tags: [ai,boost,허깅페이스, tokenizer]
toc : true
math : true
---

# tokenizer란 
- 문장을 특정규칙이있는 단어단위로 자르는거
- 이 규칙은 사전에 대용량 문서를 통해 학습하여 자르는듯하다

## 불러오기
- 허깅페이스에서 모델을 검색하여 "닉네임/repo이름"으로 허깅페이스에서 바로 불러오는게 가능하다.
- ```pthon
    MODEL_NAME = "kykim/bert-kor-base"
    tokenizer = AutoTokenizer.from_pretrained(MODEL_NAME)
  ```
## 출력 확인하기
- encode
  - `tokenizer.encode("문장")`
  - tokenizer의 규칙에 따라 자르고, id를 list로 보여준다.
  - 앞뒤로는 [cls]토큰, [sep]토큰이 따라온다.
- decode
  - `tokenizer.encode([토큰 id list])`
  - tokenizer가 가진 토큰의 id리스트를 받아서 문장으로 만들어준다.
- tokenize
  - `tokenizer.encode("문장")`
  - 실제 토크나이저가 자르는 문장단위로 list로 출력한다.

## 새로운 special 토큰 추가하기
- ```
    special_tokens_dict = {'additional_special_tokens': ['[EN1]', '[/EN1]', '[EN2]', '[/EN2]']}
    tokenizer.add_special_tokens(special_tokens_dict)
  ```
- 새로운 EN1과 EN2등을 추가하기위한 dict를 선언
- tokenizer.add_special_tokens를 통해 해당 추가된 스페셜토큰을 적용한다.