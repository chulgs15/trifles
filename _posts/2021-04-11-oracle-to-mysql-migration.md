---
published: true
layout: single
title: "[작성 중] Oracle To MySQL Table Migration"
comments: true
classes: wide
categories:
  - java
tags:
  - [oracle, mysql, java]
date: 2021-04-06
last_modified_at: 2021-04-06 
---
Oracle에서 MySQL로 넘어갈 때, Table Migration을 어떻게 해야할까 정리한다.
- [ ] 그림 필요

## 시작하게 된 이유

샘플 데이터가 Oracle에 있었는데, MySQL로 가져와 다시 분석을 진행하려고 했지만 Vendor가 제공하는 Convert 기능이 없었다.

기존 Oracle의 데이터를 MySQL에 Migration 을 하려고 했지만 쓸만한 Tool이 없었다.

시도한 방법

* Oracle Dump File to Mysql
  * Java 로 Oracle Dump 를 읽을 방법이 없다.
* Insert 문장을 만들어 DB 로 연결
  * Table 을 만든다.
  * Select 로 1000 건에 한번씩 파일을 만든다. (Thread-1)
  * Insert 를 실행한다.  (Thread-2)
  * Insert 가 끝나면 종료한다.


## Oracle and MySQL Column Matching

Matching은 SQLLines 의 Matching 을 사용해보기로 했다.

http://www.sqlines.com/oracle-to-mysql



## 오류

### 해결

VARCHAR(2000) 이상 컬럼은 모두 text 로 변경.

* https://stricky.tistory.com/297
  * mysql ERROR 1118 row size too large 오류 해결 방안
  * SHOW VARIABLES LIKE 'innodb_file_format';
  * https://mariadb.com/kb/en/troubleshooting-row-size-too-large-errors-with-innodb/
    * 공식 문서 

* https://stackoverflow.com/questions/23670855/how-to-change-read-only-permission-to-set-new-value-of-mysql-server-system-varia
  * read-only permission
* https://hub.docker.com/_/mariadb
* https://emunhi.com/view/201812/02154627862?menuNo=10031
  * 백업앤 리커버리.







## 의문점

- [x] Table 의 스키마 정보를 Select 로 가져올 수 있는가?
  - [ ] Table에서 데이터를 가져와서 
- [ ] Clob => LongText 로 Insert가 가능한가?
- [ ] Blob 정보를 가져올 수 있는가?

### 할일

- [x]  jdbc 연결
- [ ]  Mysql Connection 을 pool 형식으로 만들어서 비동기로 실행.