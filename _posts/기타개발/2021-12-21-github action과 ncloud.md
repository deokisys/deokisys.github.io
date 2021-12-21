---
layout: post
title: github action과 ncloud
categories: 기타개발
tags: [github,action,cloud,object storage,bucket]
toc : true
math : true
---


## action에서 ncloud로 배포
- object storage필요
- action build까지 완료후 해당 object storage로 파일 옮기는 것

### 사전 필요한 정보
- 인증키 만들기
  - Access Key ID/Secret Key	
  - 두개의 키가 필요하다.
- storage만들기
  - Object Storage 의 Bucket Management로 bucket 생성 [bucket](https://console.ncloud.com/objectStorage/objectStorageList)

### 인증키 만들기
- [ncloud인증키 만들러 가기](https://www.ncloud.com/mypage/manage/authkey)
  - 안들어가지면 콘솔에서 프로필클릭 - 계정관리 - 인증키 관리 - 신규 API 인증키 만들기
- ![키 확인](https://user-images.githubusercontent.com/24247768/146685967-80de0d40-7b9a-4f24-b07e-d5041981c7ca.png)
  - `Access Key ID`와 `Secret Key`	를 메모한다.

### object storage만들기
- ncloud 콘솔로 이동
- ![버킷만들기](https://user-images.githubusercontent.com/24247768/146686013-43c591f5-53b7-41c7-ba6d-f3f32d1837ef.png)
  - 버킷생성
  - 공개 여부는 일단 전체 공개로 진행
  - 버킷의 이름을 확인한다.
- 정적 웹 사이트 호스팅
  - 빌드한것을 웹사이트로 사용시 체크
  - 버킷 정보 -> 쭉 내려가서 정적 웹사이트 호스팅 클릭 -> 정적 웹사이트 활성화 -> 인덱스 파일 index.html 작성
  - ![활성및인덱스파일](https://user-images.githubusercontent.com/24247768/146686284-6e948e81-6e70-4d46-b763-e7cedd461656.png)
  - `버킷 웹 사이트 엔드포인트`를 통해 웹사이트 이용 가능

### action에서 ncloud 접속을 위한 키 저장
- 위에서 생성한 인증키는 숨겨야 한다.
  - `secrets`를 사용
- AWS_ACCESS_KEY_ID에는 Access Key ID의 값을 넣어준다.
- AWS_SECRET_ACCESS_KEY에는 Secret Key의 값을 넣어준다.

### action에서 ncloud 버킷 접속
- .github/workflows에 action에서 작성
  - 사전에 필요한 [기본 action사용법](https://deokisys.github.io/기타개발/2021/12/20/github-action쓰기.html)확인
- [aws cli관련 기본 명령어](https://cli.ncloud-docs.com/docs/guide-objectstorage) 참고 하기
- endpoint-url로 https://kr.object.ncloudstorage.com 를 가진다.

```yml
jobs:
  jobid:
   ...
   steps:
    ...
    - name: Deploy                  
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      run: |
        aws --endpoint-url=https://kr.object.ncloudstorage.com  
        s3 cp --recursive <빌드한 폴더> 
        s3://<버킷이름> 
```
  - steps내에서 사용함
  - secrets를 통해 접속에 필요한 키를 입력
  - endpoint-url로 aws의 지역적인 서비스 접근
    - ncloud는 kr.object.ncloudstorage.com
  - cp 명령어는 `cp [옮길파일] [목적지]` 복사하는 명령어
  - 위에서 만든 버킷의 이름을 사용한다.
  - 이를 통해 빌드된 값들을 복사하여 해당 버킷으로 저장하는게 가능해진다.

### 빌드 확인
- 빌드한후 확인은 `버킷 웹 사이트 엔드포인트`로 접속하여 확인한다.

## 참고
- deploy관련
  - https://velog.io/@loakick/Github-Action-AWS-S3에-React-프로젝트-배포하기
- ncloud object storage
  - https://api.ncloud-docs.com/docs/common-objectstorageapi-objectstorageapi
  
- aws cli관련
  - https://cli.ncloud-docs.com/docs/guide-objectstorage

- 엔드포인트
  - https://docs.aws.amazon.com/ko_kr/general/latest/gr/rande.html