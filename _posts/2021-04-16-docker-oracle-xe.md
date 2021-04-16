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
---
Docker Oracle XE image를 사용해 SQLDeveloper 로 접속해보자.

* 누가 언제 어디서 무엇을 어떻게 왜

* 목적
  * 실습을 위해 데이터베이스를 OS에 설치하지 않고 Docker 이미지로 실행하는 실습을 진행한다.
  * 이러면 Oracle XE 설치를 위해 파일 다운로드 및 설치에 걸리는 시간을 줄일수 있을 거라 본다. 
  
* Docker 
  * 이미지 생성
  
    * ```
      docker pull deepdiver/docker-oracle-xe-11g
      ```
  
  * Docker 실행

    * ```
    docker run -d -p 49999:1521 deepdiver/docker-oracle-xe-11g
      ```
  
* SQL Developer 접속

  * 접속은 별 거 아니었음.

* 테이블 스페이스를 Local 로 설정

  * volumn 을 올려 놓으면?

* 이미지를 지우고 다시 만들어서 접속해보기.

  * 똑같이 접속이 되는 것을 확인.



## 참조

* https://clearstar0817.tistory.com/10
  * docker 이미지
* https://m.blog.naver.com/PostView.nhn?blogId=sangminny&logNo=110011297334&proxyReferer=https:%2F%2Fwww.google.com%2F
  * userprofile 생성.