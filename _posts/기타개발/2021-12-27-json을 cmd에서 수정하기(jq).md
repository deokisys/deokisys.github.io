---
layout: post
title: json을 cmd에서 수정하기(jq)
categories: 기타개발
tags: [github,json,jq]
toc : true
math : true
---

## jq로 json수정하기
- jq라는 것을 이용해 cli에서 json파일을 읽고, 수정도 가능

### 읽기
- `jq '.homepage' package.json`
  - package.json에서 homepage를 읽을수 있다.
- `jq '.scripts.start' package.json`
  - scripts의 start를 읽는다.
- `jq '.data[1]' test.json`
  - data:[...]처럼 되어있으면 배열처럼 읽는것도 가능

### 삭제
- `jq 'del(.homepage)' package.json`
  - 삭제했을때의 결과를 출력한다. 

### 추가
- `jq '.homepage |= . + "https://deokisys.github.io/2019-11"' package.json`
  - hompage라는 키와 "https://deokisys.github.io/2019-11"라는 값을 넣는다.
  - 만약 homepage가 존재하면 이어서 값이 작성된다.


### 삭제와 추가 반영하기
- 위의 명령들은 그저 읽어서 출력만 해주며 원본에는 영향이 없다.
- 명령의 결과를 다시 원본 파일에 덯어써야한다.
- echo를 활용한다.
  - ``` echo `적용할 jq명령` > package.json ```



## 참고

- https://stackoverflow.com/questions/42245288/add-new-element-to-existing-json-array-with-jq
- https://stackoverflow.com/questions/61919141/read-json-file-in-github-actions/61919791