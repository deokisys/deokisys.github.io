---
layout: post
title: github페이지를 action으로 한번에
categories: 웹
tags: [github,gh-pages,action]
toc : true
math : true
---


## action 활용
- [이전]()에는 직접 로컬에서 빌드하고 깃헙 페이지로 넘겨야 되는 번거로움있는데, 이를 action이 자동으로 하도록 해보자.

### market에 있는거 활용
- [제작자 참고](https://github.com/marketplace/actions/github-pages-action)
- publish_dir는 빌드된 폴더의 경로
  - defaults의 run - working-directory는 반영이 안되니 직접 경로 작성
- github_token의 secrets.GITHUB_TOKEN은 자동으로 만들어지므로 저렇게 사용하면 알아서 값이 들어간다.

```yml
 - name: 깃 페이지 테스트
    uses: peaceiris/actions-gh-pages@v3.7.3
    with:
      github_token: ${{ secrets.GITHUB_TOKEN }}
      publish_dir: client/build
```

### 페이지 반영
- 만약 위의 저 명령도 성공했는데 반영이 안되었을겨우 확인할것
- repository 설정에서 pages에 gitpage source를 `gh-pages`로 설정해준다.
  - `gh-pages`는 위의 명령어가 수행되면 알아서 branch를 생성해놨을것이다.
  - ![확인](https://user-images.githubusercontent.com/24247768/146903609-8f9df666-ba4a-4aeb-b2b6-7bec7af3ba69.png)
- package.json확인
  - homepage: "https://이름.github.io/레포"을 추가해준다.
    - `%PUBLIC_URL%`에 영향을 준다.
  - action내에서 수정하고싶을때
    - `jq`활용하기



## 참고
- https://github.com/marketplace/actions/github-pages-action
- https://davidyang2149.dev/front-end/github-actions를-이용하여-gh-pages-자동-배포하기/