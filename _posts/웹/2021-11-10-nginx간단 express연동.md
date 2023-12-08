---
layout: post
title: nginx간단 express연동
categories: 웹
tags: [nginx, express]
toc: true
math: true
---

## nginx

- 마치 프록시 서버처럼 express앞단에서 작동하는 서버로서 사용

### 설정법

- `/etc/nginx/sites-available`으로 이동
- 설정파일 생성(파일명만 있으면되며 확장자는 필요없다.) ex)example
  - 아래 내용 입력

```
server {
    listen 80;
    server_name _;
    # `_`는 따로 도메인이 없을때 적어주면 외부 ip로 바로 접근하게된다.
    #nginx와 도메인 주소를 연결해 주는 역할을 합니다. 외부에서 example.com으로 들어오는 도메인 주소에서
    #요청이 들어오면 로컬에서 오픈되어 있는 아래의 " proxy_pass http://127.0.0.1:3000/; " 를 포워딩해줍니다.

    location / {
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $http_host;
      proxy_set_header X-NginX-Proxy true;

      proxy_pass http://127.0.0.1:3000/;
      # server_name example.com;로 들어온 요청을 nginx가 받아, 로컬에서 오픈되어 있는
      # "proxy_pass http://127.0.0.1:3000/;"를 포워딩해줍니다.
      # 연결되는 express의 주소를 입력해주면 됩니다.

      proxy_redirect off;
    }

    gzip on;
    gzip_comp_level 2;
    gzip_proxied any;
    gzip_min_length  1000;
    gzip_disable     "MSIE [1-6]\."
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
 }
```

- `/etc/nginx/sites-enabled`에 링크를 연결해줍니다.

  - ln -s /etc/nginx/sites-available/example /etc/nginx/sites-enabled/example

- 만든 설정파일을 nginx에서 읽도록 수정해줍니다.

  - `vi /etc/nginx/nginx.conf`
  - 하단에 `/etc/nginx/sites-available/*`를 `/etc/nginx/sites-available/example`로 바꿔줍니다.
  - `*`로 되어있으면 기본인 default를 포함해 전부 읽게 되므로 수정한 example만 읽게 고정해주는방식
  - 만약 다른 도메인을 여러개 사용할때는 \*로 해서 여러 파일을 읽게 하면 된다.

- nginx재시작
  - service nginx reload
  - /etc/init.d/nginx restart
  - reload를 권장한다한다.

### 출처

- https://valuefactory.tistory.com/165
  - 기본틀은 이 블로그에서 사용
- https://wikidocs.net/7377
