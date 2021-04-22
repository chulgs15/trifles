---
published: true
layout: single
title: "[Database] Docker Mariadb image 접속"
comments: true
categories:
  - database
tags:
  - [mariadb, mysql , docker]
date: 2021-04-18
last_modified_at: 2021-04-18
toc: true
toc_sticky: true
---
Docker Mariadb image를 사용해 heidisql 로 접속해보자.

<img src="https://user-images.githubusercontent.com/22446581/115126676-c7acb100-a00b-11eb-9f96-bd1991a008b2.png" alt="image" style="zoom:33%; display: block; margin: 0px auto;" />

## 목적
  테스트를 위해 데이터베이스를 OS에 설치하지 않고 Docker 이미지로 실행하는 실습을 진행한다. [이전 Post](https://chulgs15.github.io/trifles/database/docker-oracle-xe/)의 내용을 읽고 오면 더 이해하기 쉽다.

## Docker
 Docker 로 이미지를 생성 후 실행해보자.

### 1. 이미지 생성.

아래 명령어를 실행해서 Docker 공식 mariadb 이미지를 가져온다. 
```bash
C:\Users\사용자\Desktop\docker-명령어\mariadb>docker pull mariadb
Using default tag: latest
latest: Pulling from library/mariadb
a70d879fa598: Pull complete
c4394a92d1f8: Pull complete
10e6159c56c0: Pull complete
6ee25b525863: Pull complete
75bac9accf15: Pull complete
5553d41e82b1: Pull complete
0df5d11e9502: Pull complete
b61f99a83efe: Pull complete
6fd69eaf8690: Pull complete
03f7bd4f1dda: Pull complete
6ce5fa137d06: Pull complete
689a8c518900: Pull complete
Digest: sha256:9c681cefe72e257c6d58f839bb504f50bf259a0221c883fcc220f0755563fa46
Status: Downloaded newer image for mariadb:latest
docker.io/library/mariadb:latest
```


### 2. 이미지 실행.
아래 명령어를 실행해서 가져온 이미지를 실행한다. 이전 Oracle과 마찬가지로 현재 폴더를 Volumn으로 지정한다.

```
docker run -d -p 127.0.0.1:3306:3306 -v %cd%:/var/lib/mysql -e MYSQL_ROOT_PASSWORD='원하는 비밀번호' mariadb --character-set-server=utf8mb4
```

## Heidisql

| 설정                     | 값              |
| ------------------------ | --------------- |
| 호스트 이름              | localhost       |
| 포트                     | 3306            |
| 사용자 이름              | root            |
| 비밀 번호(초기 비밀번호) | 입력한 비밀번호 |

![image](https://user-images.githubusercontent.com/22446581/115126469-28d38500-a00a-11eb-91de-9e4fd67ab313.png)

## 참조

* https://hub.docker.com/_/mariadb
* https://m.blog.naver.com/dbwodlf3/221823831023