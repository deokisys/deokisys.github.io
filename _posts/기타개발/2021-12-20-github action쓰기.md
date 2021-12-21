---
layout: post
title: github action쓰기
categories: 기타개발
tags: [github,action,CI,CD,secrets,action env]
toc : true
math : true
---

## github의 action
- github에서 제공하는 CI/CD라고 한다.
  - continuous integration(지속적인 통합)/continuous delivery (지속적인 제공,배포)
  - private에서 무료!
- CI/CD란?
  - 애플리케이션 개발 단계를 자동화하여 애플리케이션을 보다 짧은 주기로 고객에게 제공하는 방법
  - 쉽게 말해 특정 행동을 위해 자동으로 동작하도록 스크립트를 만드는것
- 더 찾아보기
  - github action공식 문서와 git에 잘 정리되어있다.
  - 일부 사용법에 대해서는 찾기 편한듯
  - [공식문서](https://docs.github.com/en/actions)
- github action의 yml관련하여 따로 저장된 market이 존재한다. 예전 블로그에서 소개를 하고 사용하게 된다면, 제작자 페이지로 이동해서 최신 버전을 확인해서 사용하도록 하자.
  - 최신버전에서 추가 기능이 있거나 버그픽스된 버전이 존재할 수 있기 때문
  - ![최신버전확인](https://user-images.githubusercontent.com/24247768/146880391-0c1787cf-2f86-4b38-8ee6-cd4437ef5d3c.png)
  - 우측 상단의 초록 버튼 누르면 바로 확인 가능
 

### 작성 방법
- git이 설치된 폴더의 root에 `.github/workflows`로 이동 또는 생성
- 원하는 action을 yml로 정리하여 작성한다.

### 기본 action 탬플릿.yml
- node설치, node설치 캐싱, 특정 폴더 이동, 환경변수 지정, 특정 브랜치 이동

```yml
name: react buld                     # Workflow 이름

# actionTEST라는 브랜치에 이벤트 발생시 작동
on: 
  push:                           # Event 감지
    branches :
      - actionTEST # 해당 브랜치에서 push가 일어날때 실행

# 기본 설정으로 특정 폴더를 기점으로 작동하도록 하는 환경변수이다.
# 이건 전역 환경 변수라고 생각하면 될듯
defaults: 
  run: 
    working-directory: ./client # 실행되는 폴더 경로

jobs:                                 # Job 설정
  build:                              # Job ID로 여러개 만들 수 있다.
    name: react build start        # Job 이름
    # Job 가상환경 인스턴스 `ubuntu-18.04` or `ubuntu-latest`로 지정가능
    # ubuntu-latest는 ubuntu-20.04가 깔린다고한다[2021,12월 기준]
    # https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#jobsjob_idruns-on
    # 윈도우, 맥도 가능하다고...
    runs-on: ubuntu-18.04    

    # 해당 build에서 사용하는 환경변수 선언
    env:
      # Treating warnings as errors because process.env.CI = true. 에러 나올경우 고려  
      # CI관련된 에러로 문법관련 경고를 무시한다는 의미의 환경변수
      # CI에서 문법 검사를 필요할경우 기본 옵션인 true로 써야한다.
      CI: false  # 어찌보면 편법, 기본적으로 true로 사용하기
      working-directory: ./client # 실제로 작동하지는 않고 필요할때 변수처럼 사용 ${{env.working-directory}}

    # 변수 선언하는 영역으로 matrix를 통해 관리
    strategy:
      matrix:
        node-version: [12.13.0]     # 12.13.0를 변수로 저장

    steps:                            # Steps
      # 특정 브랜치의 정보 가져오기(ex actionTEST로 checkout)
      - name: checkout source code    
        uses: actions/checkout@v2  
        with:
          ref: 'actionTEST'
        
      # node설치 https://github.com/actions/setup-node
      # + node 캐시
      - name: use node version ${{matrix.node-version}} # 노드 버전 설치
        uses: actions/setup-node@v2
        with:
          node-version: ${{ matrix.node-version }}
          # setup-node에서 이젠 캐시도 지원 npm, yarn, pnpm(v6.10+)
          # 캐시는 옵션으로, 작성 안하면 캐시 작동 안함
          cache: 'yarn' 
          # 기본적으로 package-lock.json, yarn.lock을 찾지만 만약 따로 관리하는게 있다면 여기서 특정 파일을 지정하는것도 가능
          # 따로 파일 관리 안하면, 기본 옵션으로 자동으로 잘 찾아준다.
          cache-dependency-path: ${{env.working-directory}}/yarn.lock
      
      - name: yarn install           # 설치
        run: yarn install
        # 만약 특정 폴더로 이동하고 위의 run을 작동 시키고 싶을때 working-directory를 선언
        # working-directory: somedir   

      - name: yarn build              # 빌드시작
        run: yarn build  

```

### jobs
- jobs는 여러개의 jobid를 가진다
- 위는 build라는 jobid만 작동을 하지만 또다른 job을 선언하여 작동할 수 있다는 의미
- 다만 순서나 의존을 추가하기 위해서 `needs`를 통해 조절이 가능
- [문서](https://docs.github.com/en/actions/learn-github-actions/managing-complex-workflows#creating-dependent-jobs)
- 실제 사용하니까 각 job마다 환경이 다르게 작동하는듯 하다... 좀더 확인이 필요할듯하다

```yml
jobs:
  setup:
    runs-on: ubuntu-latest
    steps:
      - run: ./setup_server.sh
  build:
    needs: setup
    runs-on: ubuntu-latest
    steps:
      - run: ./build_server.sh
  test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - run: ./test_server.sh

```

### 캐쉬
- node_moduls를 저장하여 install하는 시간을 단축
  - 이전에는 actions/cache@v2를 통해 node_moduls를 캐쉬했지만 이제는 actions/setup-node@v2에서 기본 제공해줌
  - cache에서 캐시할거 선택 npm, yarn, pnpm가능
  - cache-dependency-path로 yarn.lock, package-lock.json의 경로 지정 해줄수 있다.
    - `defaults - run - working directory의 환경변수`로 경로가 알아서 바뀌지 않아서 특정폴더에서 진행하게된다면 직접 지정해줘야한다.
    - 예로 위의 yml에서 client폴더를 기본으로 지정했지만 cache를 사용시에는 해당 환경변수를 안받는것으로 보인다.
- 처음 돌릴때
  - `yarn cache is not found`
  - 시간도 대략 1분 걸림
- 그 이후
  - `Cache restored successfully`
  - 캐쉬된것을 불러오는것을 볼 수 있다.
  - install 이 1초로 단축되었다
    - 캐시되는게 약간 버그인지 딜레이가 있는지 한 2-3번 이후부터 캐시가 되어 빨라진다.


### 에러
- build시 CI관련 에러

```
  Treating warnings as errors because process.env.CI = true. 
  Most CI servers set it automatically.

  Failed to compile.
  ...
  error Command failed with exit code 1.
```

- 경고로 나오는것을 에러로 감지하고 컴파일 실패로 처리했다는 의미
  - `CI = false`를 환경변수 추가한다.
  - CI를 확인안한다는 의미
  - 만약 위의 에러가 떴다는것은 문법적으로 안이쁜게 있다는 의미로 코드에서 eslint관련 에러가 없으면 통과한다.

```yml
...
jobs:
  jobid:
  ...
    env:
      CI: false 
```

## github action에서 .env적용하기
- 현재 env를 이용해서 react를 빌드하고 특정 키나 url를 devlop과 production으로 나눠서 관리를 하고있다.
- 실제 git에는 .env파일은 .gitignore로 제외하여 올라가기 때문에 action내에서 직접 .env를 만들어야한다.
- .env파일 내용을 바로 올리면 안되는 정보이기 때문에 secrets를 이용
- `일단 나와있는 모든 방법을 사용했지만 적용이 안됨...`

### secrets사용
- github -> settings -> secrets로 이동
  - ![설정의 secrets](https://user-images.githubusercontent.com/24247768/146713198-152a7bd5-956b-445b-a432-3a959c1c70a0.png)

- `new repository secret`으로 action에서 사용하는 숨겨진 환경변수를 생성한다.
  - ![키 예시](https://user-images.githubusercontent.com/24247768/146713261-0d1cc998-7930-4d98-a590-7a9145d7100b.png)
- 이를 통해 action의 yml에서는 `${{secrets.AWS_ACCESS_KEY_ID}}`로 해당 값을 불러오는게 가능해진다.
- action작동시에는 secrets의 키에 해당하는 값은 자동적으로 ***로 암호화해서 로그에는 보이지만 실제로 잘 저장되고 있다.

### env 방법1
- run build시에 env적용하기
  - build할때 env로 바로 넘기기

```yml
- name:  build            # 빌드시작
  env:
    # run: env-cmd -f .env react-scripts build
    REACT_APP_DEV_API: ${{ secrets.REACT_APP_DEV_API}}
    REACT_APP_DEV_CHAT: ${{ secrets.REACT_APP_DEV_CHAT}}
    ## Firebase
    REACT_APP_DEV_API_KEY: ${{ secrets.REACT_APP_DEV_API_KEY}}
    REACT_APP_DEV_AUTH_DOMAIN: ${{ secrets.REACT_APP_DEV_AUTH_DOMAIN}}
    REACT_APP_DEV_DATABASE_URL: = ${{ secrets.REACT_APP_DEV_DATABASE_URL}}
    REACT_APP_DEV_PROJECT_ID: = ${{ secrets.REACT_APP_DEV_PROJECT_ID}}
    REACT_APP_DEV_STORAGE_BUCKET: = ${{ secrets.REACT_APP_DEV_STORAGE_BUCKET}}
    REACT_APP_DEV_MESSAGING_SENDERID: = ${{ secrets.REACT_APP_DEV_MESSAGING_SENDERID}}
    REACT_APP_DEV_APP_ID: ${{ secrets.REACT_APP_DEV_APP_ID}}
    REACT_APP_DEV_MEASUREMENT_ID:  ${{ secrets.REACT_APP_DEV_MEASUREMENT_ID}}

    REACT_APP_PROD_API: ${{ secrets.REACT_APP_PROD_API}}
    REACT_APP_PROD_CHAT: ${{ secrets.REACT_APP_PROD_CHAT}}
    ## Firebase
    REACT_APP_API_KEY: ${{ secrets.REACT_APP_API_KEY}}
    REACT_APP_AUTH_DOMAIN: ${{ secrets.REACT_APP_AUTH_DOMAIN}}
    REACT_APP_DATABASE_URL: ${{ secrets.REACT_APP_DATABASE_URL}}
    REACT_APP_PROJECT_ID: ${{ secrets.REACT_APP_PROJECT_ID}}
    REACT_APP_STORAGE_BUCKET: ${{ secrets.REACT_APP_STORAGE_BUCKET}}
    REACT_APP_MESSAGING_SENDERID: ${{ secrets.REACT_APP_MESSAGING_SENDERID}}
    REACT_APP_APP_ID: ${{ secrets.REACT_APP_APP_ID}}
    REACT_APP_MEASUREMENT_ID: ${{ secrets.REACT_APP_MEASUREMENT_ID}}
    REACT_APP_KAKAO_KEY: ${{ secrets.REACT_APP_KAKAO_KEY}}
    REACT_APP_GOOGLE_KEY: ${{ secrets.REACT_APP_GOOGLE_KEY}}
    DEV_BUCKET: ${{ secrets.DEV_BUCKET}}
    ##oauth
    REACT_APP_DEV_KAKAO_KEY: ${{ secrets.REACT_APP_DEV_KAKAO_KEY}}
    REACT_APP_DEV_OAUTH_KAKAO_KEY: ${{ secrets.REACT_APP_DEV_OAUTH_KAKAO_KEY}}
    REACT_APP_DEV_OAUTH_GOOGLE_KEY: ${{ secrets.REACT_APP_DEV_OAUTH_GOOGLE_KEY}}
  run: yarn run build
```

### env 방법2
- 직접 .env파일을 만들기

```yml
- name: .env파일 만들기
  env:
    REACT_APP_DEV_API: ${{ secrets.REACT_APP_DEV_API}}
    REACT_APP_DEV_CHAT: ${{ secrets.REACT_APP_DEV_CHAT}}
    ## Firebase
    REACT_APP_DEV_API_KEY: ${{ secrets.REACT_APP_DEV_API_KEY}}
    REACT_APP_DEV_AUTH_DOMAIN: ${{ secrets.REACT_APP_DEV_AUTH_DOMAIN}}
    REACT_APP_DEV_DATABASE_URL: = ${{ secrets.REACT_APP_DEV_DATABASE_URL}}
    REACT_APP_DEV_PROJECT_ID: = ${{ secrets.REACT_APP_DEV_PROJECT_ID}}
    REACT_APP_DEV_STORAGE_BUCKET: = ${{ secrets.REACT_APP_DEV_STORAGE_BUCKET}}
    REACT_APP_DEV_MESSAGING_SENDERID: = ${{ secrets.REACT_APP_DEV_MESSAGING_SENDERID}}
    REACT_APP_DEV_APP_ID: ${{ secrets.REACT_APP_DEV_APP_ID}}
    REACT_APP_DEV_MEASUREMENT_ID:  ${{ secrets.REACT_APP_DEV_MEASUREMENT_ID}}

    REACT_APP_PROD_API: ${{ secrets.REACT_APP_PROD_API}}
    REACT_APP_PROD_CHAT: ${{ secrets.REACT_APP_PROD_CHAT}}
    ## Firebase
    REACT_APP_API_KEY: ${{ secrets.REACT_APP_API_KEY}}
    REACT_APP_AUTH_DOMAIN: ${{ secrets.REACT_APP_AUTH_DOMAIN}}
    REACT_APP_DATABASE_URL: ${{ secrets.REACT_APP_DATABASE_URL}}
    REACT_APP_PROJECT_ID: ${{ secrets.REACT_APP_PROJECT_ID}}
    REACT_APP_STORAGE_BUCKET: ${{ secrets.REACT_APP_STORAGE_BUCKET}}
    REACT_APP_MESSAGING_SENDERID: ${{ secrets.REACT_APP_MESSAGING_SENDERID}}
    REACT_APP_APP_ID: ${{ secrets.REACT_APP_APP_ID}}
    REACT_APP_MEASUREMENT_ID: ${{ secrets.REACT_APP_MEASUREMENT_ID}}
    REACT_APP_KAKAO_KEY: ${{ secrets.REACT_APP_KAKAO_KEY}}
    REACT_APP_GOOGLE_KEY: ${{ secrets.REACT_APP_GOOGLE_KEY}}
    DEV_BUCKET: ${{ secrets.DEV_BUCKET}}
    ##oauth
    REACT_APP_DEV_KAKAO_KEY: ${{ secrets.REACT_APP_DEV_KAKAO_KEY}}
    REACT_APP_DEV_OAUTH_KAKAO_KEY: ${{ secrets.REACT_APP_DEV_OAUTH_KAKAO_KEY}}
    REACT_APP_DEV_OAUTH_GOOGLE_KEY: ${{ secrets.REACT_APP_DEV_OAUTH_GOOGLE_KEY}}
  run: |
    echo REACT_APP_DEV_API = $REACT_APP_DEV_API >> .env
    echo REACT_APP_DEV_CHAT = $REACT_APP_DEV_CHAT >> .env

    echo REACT_APP_PROD_API = $REACT_APP_PROD_API >> .env 
    echo REACT_APP_PROD_CHAT = $REACT_APP_PROD_CHAT >> .env 

    echo REACT_APP_API_KEY = $REACT_APP_API_KEY>> .env
    echo REACT_APP_AUTH_DOMAIN = $REACT_APP_AUTH_DOMAIN>> .env
    echo REACT_APP_DATABASE_URL = $REACT_APP_DATABASE_URL>> .env
    echo REACT_APP_PROJECT_ID = $REACT_APP_PROJECT_ID>> .env
    echo REACT_APP_STORAGE_BUCKET = $REACT_APP_STORAGE_BUCKET>> .env
    echo REACT_APP_MESSAGING_SENDERID = $REACT_APP_MESSAGING_SENDERID>> .env
    echo REACT_APP_APP_ID = $REACT_APP_APP_ID>> .env
    echo REACT_APP_MEASUREMENT_ID = $REACT_APP_MEASUREMENT_ID>> .env
    echo REACT_APP_KAKAO_KEY = $REACT_APP_KAKAO_KEY>> .env
    echo REACT_APP_GOOGLE_KEY = $REACT_APP_GOOGLE_KEY>> .env
    echo DEV_BUCKET = $DEV_BUCKET>> .env

    echo REACT_APP_DEV_KAKAO_KEY = ${{ secrets.REACT_APP_DEV_KAKAO_KEY}}>> .env
    echo REACT_APP_DEV_OAUTH_KAKAO_KEY = ${{ secrets.REACT_APP_DEV_OAUTH_KAKAO_KEY}}>> .env
    echo REACT_APP_DEV_OAUTH_GOOGLE_KEY = ${{ secrets.REACT_APP_DEV_OAUTH_GOOGLE_KEY}} >> .env
    
```


### 참고
- 에러
  - https://www.sarah-note.com/Error/error3/
  - https://github.community/t/treating-warnings-as-errors-because-process-env-ci-true/18032/8
  - https://sundries-in-myidea.tistory.com/105


- action관련
  - https://docs.github.com/en/actions
  - https://velog.io/@loakick/Github-Action-React-빌드하기
  - https://meetup.toast.com/posts/286
  - https://velog.io/@ggong/Github-Action에-대한-소개와-사용법
  - https://velog.io/@bluestragglr/원하는-디렉토리에서-Github-Actions-실행하기

- env관련
  - https://ji5485.github.io/post/2021-06-26/create-env-with-github-actions-secrets/
  - https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/GitHub-Actions-Secrets-Example-Token-Tutorial
  - https://velog.io/@heyoon/github-actions를-이용해-빌드-배포된-서버에서-환경변수process.env.환경변수를-사용하려면-어떻게-해야-할까요
  - https://hini7.tistory.com/172
  - https://forhjy.medium.com/ci-cd-github-actions로-env-dotenv-파일-만들기-4592a6720c6d
  - https://ji5485.github.io/post/2021-06-26/create-env-with-github-actions-secrets/


- CI/CD
  - https://www.redhat.com/ko/topics/devops/what-is-ci-cd