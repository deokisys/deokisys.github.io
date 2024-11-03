---
layout: post
title: webserver와 WAS
categories: 공부
tag: [web server, was, Web Application Server, nginx]
toc: true
---

# webserver, was

- 웹서버와 WAS의 차이를 정리해 보자

## 웹 서버(Web Server)

- 클라이언트의 request(요청)을 받아 **`*정적 컨텐츠*`**를 response(응답)하는 서버
  > **_정적 컨텐츠_**
  > 단순 HTML 문서, CSS, 이미지, 파일 등 즉시 응답 가능한 컨텐츠
- 기능
  1. 정적 컨텐츠 제공: WAS를 거치지 않고 바로 자원 제공
  2. 동적 컨텐츠를 위한 요청 전달: 클라이언트 요청을 WAS에 보내고, WAS에서 처리한 결과를 클라이언트에 전달
- Apache, NginX, IIS, WebtoB 등
  ![static pages](https://github.com/user-attachments/assets/984743fd-2775-4744-ba80-586e93f0fdba)

## WAS(Web Application Server)

![was](https://github.com/user-attachments/assets/f109362d-ae5e-44c9-bde3-afa1cfbe131f)

- 클라이언트의 request(요청)을 받아 DB 조회나, 어떤 로직을 처리해야하는 **`*동적 컨텐츠*`**를 response(응답)하는 서버
- HTTP를 통해 애플리케이션을 수행해주는 미들웨어
- 역할
  ```
  WAS = Web Server + Web Container
  ```
- **WAS는 웹 컨테이너 혹은 서블릿 컨테이너**라고도 불림
  > **_컨테이너_**
  > JSP, Servlet을 실행시킬 수 있는 소프트웨어. 즉, WAS는 JSP, Servlet 구동 환경을 제공
  - 웹 컨테이너의 작동
    ![웹 컨테이너](https://github.com/user-attachments/assets/ad65493a-d12c-43d7-bd97-d1596ec65a76)
    1. 클라이언트는 웹서버로 `request`(요청)을 보낸다.
    2. 서블릿을 포함하는 WAS는 컨테이너로 요청을 보낸다.
    3. 컨테이너가 요청을 각 서블릿에게 전달한다.
    4. 서블릿 메서드가 로드된다.
    5. 서블릿은 컨테이너에 관련 `response`(응답)을 넘겨준다.
    6. 컨테이너는 이를 서버에 전달한다. 서버는 응답을 클라이언트에게 전달한다.
- 기능
  1. 프로그램 실행 환경 및 DB 접속 기능 제공
  2. 여러 트랜잭션 관리 기능
  3. 업무 처리하는 비즈니스 로직 수행
- Tomcat, WebLogic, WebSphere, Jeus, JBoss 등

### `Web Server` vs `WAS`

- 결론적으로, 둘의 차이는 정적 or 동적 컨텐츠를 제공하느냐이다.
- Web Server와 WAS는 각각 독립적으로 존재할 수 있다.
  - springboot는 웹 어플리케이션을 내장된 톰캣(was)으로 실행하는 데 web server가 필요하지 않다

### Web Server를 사용하는 이유 ?

WAS가 웹 서버를 포함하는 개념이라면, 왜 굳이 웹 서버와 WAS를 같이 사용하는 것일까?

1. WAS의 부담의 줄이기 위해

   WAS 앞에 웹 서버를 둬서 웹 서버에 **_정적_**인 문서만 처리하도록하고, WAS는 애플리케이션의 로직만 수행하도록 기능을 분배해서 서버의 부담을 줄인다.

   ![web](https://github.com/user-attachments/assets/8715e52e-b6a6-44dd-9df6-d3b6929fe677)

2. WAS의 환경 설정 파일을 외부에 노출시키지 않도록 하기 위해서

   1. 클라이언트와 연결하는 포트가 직접 WAS에 연결이 되어 있다면 중요한 설정 파일들이 노출될 수 있기 때문에 WAS 설정 파일을 외부에 노출시키지 않도록 하기 위함
   2. 웹 서버와 WAS에 접근하는 포트가 다르기 때문에 WAS에 들어오는 포트에는 방화벽을 쳐서 보안을 강화할 수도 있다.

   > 우리가 웹 어플리케이션을 개발할 때 사용하는 `Apache Tomcat`은 WAS(Tomcat)가 웹 서버(Apache) 기능을 포함하고 있기 때문에 `Apache Tomcat`이라고 부르기도 하고, 실제로 WAS 앞에 웹 서버를 두어서 `Apache Tomcat`이라고 부르기도 한다.

### apach tomcat만 쓰면 되는데 왜 nginx를 쓰나?

- nginx를 같이 쓰는 이유는 여러 이점이 존재한다.
  - 로드밸런싱과 성능향상이 있다.
- web 서버의 기능
  - 캐싱
    - get같은 자주 데이터를 요청하는것은 캐싱
  - 로드밸런서
    - 웹서버를 따로 둔다면, nginx가 로드밸런서 역할이 된다.
    - nginx설정으로 로드밸런스 설정이 가능하다.
  - 리버스 프록시
    - ip를 공개하지 않을수 있음
- was만 쓴다면?
  - 무한로딩으로 터질것이다.
  - 웹서버가 있다면?
    - 정적이 가능하므로 잘 죽지 않음
    - was는 코드로 작동하므로 서버가 죽을 수 있다.
    - 문제가 발생한다면, 정적페이지에서 에러 처리도 가능하다.
- 3tier 구조: Web Server - WAS - DB

  - nginx는 웹서버로서 프레젠테이션 계층에서 수행하는 중요한 역할을 해준다.

  - **프레젠테이션 계층 (Presentation Tier)**

    사용자가 직접 마주하게 되는 계층이다. 따라서 주로 사용자 인터페이스(인터넷 브라우저 등)를 지원하며 이 계층은 GUI 또는 프론트엔드(front-end) 라고도 부른다. 그러므로 이 계층에서는 사용자 인터페이스와 관계없는 데이터를 처리하는 로직은 포함하지 않는다. 주로 `웹 서버`를 예시로 들 수 있고, HTML, Javascript, CSS 등이 이 계층에 해당 된다.

  - **어플리케이션 계층 (Application Tier)**

    이 계층에서는 (프레젠테이션 계층) 요청되는 정보를 어떠한 규칙을 바탕으로 처리하고 가공하는 것들을 담당한다. (동적인 데이터 제공!) 비즈니스 로직 계층 또는 트랜잭션 계층 이라고도 한다. 첫 번째 계층에서 이 계층을 바라볼 때에는 서버처럼 동작하고(응답), 세 번째 계층의 프로그램에 대해서는 마치 클라이언트처럼 행동한다.(요청)
    따라서 이 계층은 미들웨어(Middleware) 또는 백엔드(back-end)라고도 불린다. 이 계층에서는 프레젠테이션코드 (예를 들면 HTML, CSS)나 데이터 관리를 위한 코드는 포함하지 않는다. 주로 PHP, Java 등이 이 계층에 해당한다.

  - **데이터 계층 (Data Tier)**
    데이터 계층은 데이터베이스와 데이터베이스에 접근하여 데이터를 읽거나 쓰는 것을 관리하는 것을 포함한다.
    주로 DBMS (Database Management System)이 이 계층에 해당된다. 데이터 계층 또한 백엔드(back-end)라고도 부른다. 주로 MySQL, MongoDB 등이 이 계층에 해당된다.

  - 참고
    [[10분 테코톡] 👩‍🦰희봉의 웹서버 vs WAS](https://www.youtube.com/watch?v=NyhbNtOq0Bc)

- nginx에서 무중단 배포를 제공해준다.
  - 하나 업데이트하면서 다른 서버에 요청을 전달하고, 업데이트가 끝나면 해당 서버에 요청을 보내고 다른 서버는 업데이트

## 참고

[WAS와 Server의 차이? 그리고 Web Container 란?](https://doozi316.github.io/web/2020/09/13/WEB26/)
