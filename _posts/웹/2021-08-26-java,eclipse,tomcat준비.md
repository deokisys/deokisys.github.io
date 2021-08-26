---
layout: post
title: java,eclipse,tomcat준비
categories: 웹
tags: [java,eclipse,tomcat,servlet]
toc : true
math : true
---


## java설치
- mac기준
- 8버전이 많이 쓰는거 같음
  - [https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)
  - 위 링크로 mac운영체제 버전 설치

### 설치 경로
- `/Library/Java/JavaVirtualMachines/`로 가면 설치된 버전 확인 가능

### 삭제
- `sudo rm -fr /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin`
- `sudo rm -fr /Library/PreferencesPanes/JavaControlPanel.prefPane`
- `sudo rm -fr ~/Library/Application\ Support/Java`


### 확인
- 버전확인
  - `java -version`

## eclipse설치
- [https://www.eclipse.org/downloads/packages/](https://www.eclipse.org/downloads/packages/)로 이동
- `Eclipse IDE for Enterprise Java and Web Developers`라고 되어있는것을 설치

### 인코딩 설정
- preference(좌측상단 eclipse를 누르면 있음)의 General-workspace로 이동
  - 하단의 text file encoding을 UTF-8로 설정
- Web-cssfiles도 UTF-8
- Web-HTMLfiles도 UTF-8
- Web-JSPfiles도 UTF-8

## tomcat설치
- mac기준, 8버전 설치
- [https://tomcat.apache.org/download-80.cgi](https://tomcat.apache.org/download-80.cgi)
  - Binary Distributions의 tar.gz를 다운로드
  - 압축해제
  - `chmod +x ./bin/*.sh`로 실행권한 주기

### 실행, 종료
- `./bin/startup.sh`
- `./bin/shutdown.sh`또는 실행한 터미널 종료

## 기본 서브릿 만들기
- 이클립스 우측 상단의 `java ee`아이콘으로 웹어플리케이션 제작에 편한 환경으로 전환
- 프로젝트 생성
  - new-> dynamic web project로 프로젝트 생성
  - 이름 지정
  - target runtime에 설치한 tomcat의 위치를 지정해준다.(압축해제한 폴더로 경로를 해주면 된다.)
- 서브릿 생성
  - src에서 우클릭
  - new -> servlet으로 servlet생성
  - package이름과 class이름을 지정해준다.
  - class이름은 기본적으로 urlmapping주소가된다.
    - `http://localhost:8080/{프로젝트이름}/{urlmapping값}`
  - 기본으로 doget만 설정한다음 생성해준다.
    - doget에 아래 내용을 입력해준다.
    - ```java
      PrintWriter out = response.getWriter();
      out.print("<h1>hi servlet</h1>");
      ```
    - 이는 get요청이 오면 실행하는 명령으로 hi servlet이라고 화면에 출력해준다.
- 서브릿 실행
  - 만들어진 java파일 우클릭, `run as`로 실행
  - 설치된 tomcat을 선택하여 실행해주면 된다.


## 참고
- [링크](https://blog.naver.com/15elly/221874152590)