---
published: false
layout: single
title: "[Java] Intellij로 Angular + Spring Boot + Docker 구성하기(2)"
comments: true
categories:
  - java
tags:
  - [serious, intellij, docker, angular, spring]
date: 2021-05-22
last_modified_at: 2021-05-22
toc: true
toc_sticky: true
---

 Intellij Enterprise 기능으로 Frontend/Backend/Docker를 구성해보자.

* 현상
  * 이전에 구성한 내용에 Docker를 추가해보겠다.
  * DB 이미지를 실행.
  * docker-compose로 로컬에서 실행해보기.
* docker-compose.yml 파일 작성
  * 파일 작성
* frontend 에 ngnix 작성.
  * 이유
    * 최종 build에는 html 문서만 존재하고, web server가 없기 때문이다.
  * 파일 작성
* DB 이미지 작성
  * mydata 폴더 생성
* nginx 구성.
  * 최종 앞단의 web server.
* 실행하기
  * docker-compose up --build

