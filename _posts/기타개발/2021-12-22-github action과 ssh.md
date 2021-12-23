---
layout: post
title: github action과 ssh
categories: 기타개발
tags: [github,action,ssh,scp,sshpass]
toc : true
math : true
---


## action에서 ssh연결
- [action 기본 참고](https://deokisys.github.io/%EA%B8%B0%ED%83%80%EA%B0%9C%EB%B0%9C/2021/12/20/github-action%EC%93%B0%EA%B8%B0.html)
- [이전](https://deokisys.github.io/%EA%B8%B0%ED%83%80%EA%B0%9C%EB%B0%9C/2021/12/21/github-action%EA%B3%BC-ncloud.html)에는 ncloud에 연결을 진행
- ssh로 자신이 가지고있는 서버에 직접 연결하여 파일 옮기는 법 확인

### ssh 연결(applebody/ssh-action)
- [제작자를 참고](https://github.com/marketplace/actions/ssh-remote-commands)
- ssh에 연결해주고 script를 실행할 수 있게 제작된 모듈같은 것
- secrets를 통해 host와 비밀번호등을 작성하면 작동한다.

```yml
- name: ssh 연결하기
  uses: appleboy/ssh-action@v0.1.4
  with:
    host: ${{ secrets.SSH_HOST }}
    username: ${{ secrets.SSH_USERNAME }}
    password: ${{ secrets.SSH_PASSWORD }}
    port: ${{ secrets.SSH_PORT }}
    script: whoami # ssh연결후 실행하고 싶은 명령어
```

### scp 연결
- 위의 모듈이 아닌 sshpass를 이용한 연결법
- `sshpass -p ${{ secrets.SSH_PASSWORD }} scp -o StrictHostKeyChecking=no -P ${{secrets.SSH_PORT}} -r [옮길 폴더] ${{secrets.SSH_USERNAME}}@${{secrets.SSH_HOST}}:[목적지폴더]`
  - 예시
    - `sshpass -p ${{ secrets.SSH_PASSWORD }} scp -o StrictHostKeyChecking=no -P ${{secrets.SSH_PORT}} -r ./build/* ${{secrets.SSH_USERNAME}}@${{secrets.SSH_HOST}}:/var/www/testtest`
    - build폴더 내용물을 /var/www/testtest로 복사
  - sshpass를 이용하여 비밀번호를 입력
  - StrictHostKeyChecking=no 이 옵션을 통해 추가적인 키 확인을 안하도록 한다.
    - 이 옵션이 없으면 키가 없다고 오류가 나온다.`Process completed with exit code 6.`
  - build폴더의 내용
  - 덮어쓰는게 안되서 목적지의 폴더 내부의 내용을 삭제해야함, 안그러면 내부에 폴더를 만들수 있고 아니면 예시처럼 *을 사용해야함


### rsync 연결
- scp보다 빠르다고 한다.
- `rsync --rsh = "sshpass -p ${{secrets.SSH_KEY}} ssh -l ${{secrets.SSH_USERNAME}} -p ${{secrets.SSH_PORT}}" build/ ${{secrets.SSH_HOST}}:/var/www/testtest/`
  - cli버전도 가능하게 하고싶었지만 작동 안됨
  - 마켓에서 작동하는 rsync를 활용[제작자](https://github.com/marketplace/actions/rsync-deployments-action)
- rsync는 폴더 하나를 통째로 동기화가 가능해서 따로 삭제를 안해도 된다는 장점
  - 끝을 `/`로 하면 해당 폴더를 동기화한다는 의미가 된다.

```yml
- name: rsync market
  uses: burnett01/rsync-deployments@5.1
  with:
    switches: -avzr
    path: client/build/
    remote_path: /var/www/testtest/
    remote_host: ${{secrets.SSH_HOST}}
    remote_user: ${{secrets.SSH_USERNAME}}
    remote_key: ${{ secrets.SSH_KEY }}
```

  - 여기서 key는 위에서 사용하던 ssh의 비밀번호가 아닌 rsa키를 만들어야한다.
    - [rsa키 만드는법](https://deokisys.github.io/기타개발/2021/12/13/ssh-rsa키-만들기.html)참고해서 SSH_KEY로 secrets에 추가
  - 경로가 defaults - run - working-directory가 적용안되므로 폴더 위치를 작성해줘야한다.

## 참고
- ssh연결
  - https://bug41.tistory.com/159
  - https://github.com/marketplace/actions/ssh-remote-commands

- scp연결
  - https://github.com/garygrossgarten/github-action-scp
  - https://github.com/appleboy/scp-action
  - https://velog.io/@tkfrn4799/Ubuntu-bash-script에서-ssh-scp-사용-시-패스워드-입력하는-방법
  - https://hyunsuk2.github.io/2018/01/16/ssh/

- rsync
  - https://noota.tistory.com/entry/rsync-파일-복사-및-삭제
  - https://github.com/marketplace/actions/rsync-deployments-action
  - https://gyuha.tistory.com/445
  - https://wi-fi.tistory.com/entry/rsync-사용법자주-사용하는-예제-모음
  - https://gkstamin.tistory.com/entry/Linux-rsync시-ssh-포트-변경-및-사용

- sshpass에러
  - https://stackoverflow.com/questions/33961214/docker-run-fails-with-returned-a-non-zero-code-6