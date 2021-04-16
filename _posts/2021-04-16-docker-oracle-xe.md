---
published: true
layout: single
title: "[Oracle] Docker Oracle XE image 접속"
comments: true
classes: wide
categories:
  - Database
tags:
  - [oracle, docker]
date: 2021-04-16
last_modified_at: 2021-04-16
toc: true
toc_sticky: true
---
Docker Oracle XE image를 사용해 SQLDeveloper 로 접속해보자.

<img src="https://user-images.githubusercontent.com/22446581/115093584-ce78ec80-9f55-11eb-8503-321d29b348b8.png" alt="image" style="zoom:33%; display: block; margin: 0px auto;" />

## 목적
  실습을 위해 데이터베이스를 OS에 설치하지 않고 Dcoker 이미지로 실행하는 실습을 진행한다. Docker 이미지를 사용할 때 장점은 Oracle XE 설치를 위해 파일 다운로드 및 설치에 걸리는 시간을 줄일수 잇따.

## Docker
 Docker 로 이미지를 생성 후 실행해보자.

### 1. 이미지 생성.

아래 명령어를 실행해서 Docker 이미지를 가져온다. Oracle 공식 이미지는 아니지만 실행 가능한 이미지다.
```
docker pull deepdiver/docker-oracle-xe-11g
```

![image](https://user-images.githubusercontent.com/22446581/115093180-78f01000-9f54-11eb-9afc-5ca4b0205057.png)


### 2. 이미지 실행.
아래 명령어를 실행해서 가져온 이미지를 실행한다.

```
docker run -d -p 49999:1521 deepdiver/docker-oracle-xe-11g
```

오라클은 Oracle Listner 를 통해 외부에서 접속해야 한다. Listner의 기본 포트는 1521번이다. Host의 49999번과 Container의 1521을 Mapping 해주기 위해서  `-p 49999:1521` 옵션을 붙인다.

 실행 시 접속 계정은 system이다. 7일 후 계정이 만료되기 때문에 접속해서 계정의 Password를 바꿔주자.

![image](https://user-images.githubusercontent.com/22446581/115093773-5ced6e00-9f56-11eb-9721-b3f99b60ed90.png){: .align-center}

## SQLDeveloper

| 설정                     | 값        |
| ------------------------ | --------- |
| 호스트 이름              | localhost |
| 포트                     | 49999     |
| 사용자 이름              | system    |
| 비밀 번호(초기 비밀번호) | oracle    |

접속 정보를 입력해서 SQL Developer 에 접속해보자.

![image](https://user-images.githubusercontent.com/22446581/115093320-dd12d400-9f54-11eb-8a6b-5d62d2735bd1.png){: .align-center}

## Volumn

Docker 는 기본적으로 데이터를 저장하지 않기 때문에, 다시 실행하면 초기화가 된다. 즉, 아무리 Schema를 만들어도 Docker를 종료하면 없어진다. Container 안에서 데이터를 저장하기 위해 Volumn을 지정하자.

```bash
docker run -p 49999:1521 -v oracle-xe deepdiver/docker-oracle-xe-11g
```

이제 Schema를 자유롭게 만들어보자.

## 참조

* https://clearstar0817.tistory.com/10
* https://m.blog.naver.com/PostView.nhn?blogId=sangminny&logNo=110011297334&proxyReferer=https:%2F%2Fwww.google.com%2F