---
layout: post
title: react nginx빌드
categories: 웹
tags: [nginx,react build]
toc : true
math : true
---


## react 빌드 후 nginx로 올리기
- react를 실행하여 nginx가 연결해주는게 아닌, react를 정적으로 빌드하여 nginx가 이 정적파일을 작동하는 방식


### react 빌드
- `yarn build`로 빌드 시작
  - `react-scripts build`와 같음
  - 주의할점 node_env는 기본적으로 production으로 진행될것이니 만약 환경변수가 제대로 작성 안되어있으면 오류가 난다.

- 에러
  - 대부분 버전문제로 알맞은 버전을 찾아야함...
  - 12.13.0이 잘맞는듯

### nginx 올리기
- [nginx연동](https://deokisys.github.io/%EC%9B%B9/2021/11/10/nginx%EA%B0%84%EB%8B%A8-express%EC%97%B0%EB%8F%99.html)와 연계되서 설정 시작
- site-available에 파일을 만들고 아래처럼 작성
  - 기본적인 설정이지만 매우 기본적이다.

```
server {
    listen 80; # 만약 포트로만 구분하려면 server_name은 지우고 포트를 수정
    server_name _; # 도메인이 있으면 구분하는 도메인 작성
    root /root/react/build;  # 빌드한 폴더 위치
    index index.html;  # 시작되는 html파일
    location / {
      try_files $uri $uri/ /index.html; # 파일을 찾는다고 한다.
  }
}
```

- react-router를 사용시 아래처럼 작성

```
server {
    listen 80; # 만약 포트로만 구분하려면 server_name은 지우고 포트를 수정
    server_name _; # 도메인이 있으면 구분하는 도메인 작성

    root /root/react/build;  # 빌드한 폴더 위치
    index index.html index.htm;      

    location ~* \.(?:manifest|appcache|html?|xml|json)$ {
      expires -1;
      # access_log logs/static.log; # I don't usually include a static log
    }

    location ~* \.(?:css|js)$ {
      try_files $uri =404;
      expires 1y;
      access_log off;
      add_header Cache-Control "public";
    }

    # Any route containing a file extension (e.g. /devicesfile.js)
    location ~ ^.+\..+$ {
      try_files $uri =404;
    }

    # Any route that doesn't have a file extension (e.g. /devices)
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```


### 에러
- 위처럼 해도 500 에러가 나는 경우 조치할 방법
  - root 경로 확인
    - root에서 경로를 잘 작성했는지 확인
  - 권한 확인
    - [권한설정](https://baengsu.tistory.com/24) 내용을 참고해서 확인
  - 경로 자체 문제
    - 나의 문제는 위 로도 해결 안되서 확인해보니 /root경로를 nginx가 못읽고 /var/www의 경로는 nginx가 읽을 수 있었다.
    - 해결방법으로 빌드된 폴더 자체를 /var/www로 복사하여 해결하였다.
    - `mv [build한 폴더] /var/www/[바꾸고싶은 이름]`로 옮겨서 사용


## 참고
- nginx에 빌드
  - https://baengsu.tistory.com/24
  - https://changrea.io/etc/nginx-config-for-react/
  - https://133hyun.tistory.com/75
  - https://www.hanumoka.net/2019/12/29/react-20191229-react-nginx-deploy/
- router있을경우
  - https://wkdtjsgur100.github.io/react-router-deploy/
  - https://www.hides.kr/931
- 추가 팁
  - https://sihus.tistory.com/31
  - https://codechacha.com/ko/deploy-react-with-nginx/
  - https://extrememanual.net/6481
