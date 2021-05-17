---
published: true
layout: single
title: "[Database] Oracle 19c 설치"
comments: true
categories:
  - database
tags:
  - [serious, database, oracle]
date: 2021-05-15
last_modified_at: 2021-05-15
toc: true
toc_sticky: true
---

 접속 테스트를 위해 Oracle 19c를 Docker로 실행.

# Oracle 19c와 JDBC 접속 버젼확인

기존 Oracle 11g를 19c Upgrade 프로젝트 중 JDBC 지원에 대한 이슈가 발생했다. jdk 1.8 이하로 만들어진 Java Web 프로그램들이 19c를 지원 여부를 확인해야 했다. Oracle의 공식 Homepage에서 확인하면 아래와 같이 나온다.

![image](https://user-images.githubusercontent.com/22446581/118341483-4be64b80-b55a-11eb-98b9-d01fc30ac3ad.png)

홈페이지 정보대로라면 대대적인 JDK 1.8 Upgrade를 해야할 상황이었다. 테스트를 위해서 Docker로 접속과 Select를 빠르게 테스트 해보았다.

# 설치 방법

## 1. Git Clone

Git Clone 을 받는다.   

  ```powershell
  PS D:\> git clone https://github.com/oracle/docker-images
  Cloning into 'docker-images'...
  remote: Enumerating objects: 13925, done.
  remote: Counting objects: 100% (205/205), done.
  remote: Compressing objects: 100% (146/146), done.
  remote: Total 13925 (delta 80), reused 135 (delta 44), pack-reused 13720
  Receiving objects: 100% (13925/13925), 9.92 MiB | 12.32 MiB/s, done.
  Resolving deltas: 100% (8131/8131), done.
  Updating files: 100% (1514/1514), done.
  ```

## 2. Oracle 19c를 다운로드 및 복사

### 다운로드

Oracle 공식 Homepage에서 다운로드한다. 여기서 중요한 점은 **꼭 Linux x86-64 버젼을 다운받아야 한다.**

![image](https://user-images.githubusercontent.com/22446581/118341846-a6cc7280-b55b-11eb-8011-2dfc5fce89c2.png)

### 복사

 다운받은 폴더를 아래 경로에 복사를 해야한다.

```powershell
D:\docker-images\OracleDatabase\SingleInstance\dockerfiles\19.3.0\
```

![image](https://user-images.githubusercontent.com/22446581/118380184-dee6ba80-b61a-11eb-8363-eac278b9e7af.png)

## 3. Docker 명령어 실행.

### Build

 아래 명령어로 Docker를 Build해야 한다. Window를 사용하고 있다면 Git bash에서 아래 명령어를 실행해야 한다.

```bash
./buildContainerImage.sh -i -e -v 19.3.0
```

![image](https://user-images.githubusercontent.com/22446581/118380344-fd998100-b61b-11eb-99a9-9c3646f4ffe9.png)

### 실행

빌드가 끝나면 아래와 같은 메시지 가 나타난다. 아래 메시지의 Image를 사용해서 `Docker run`을 실행한다.

```
  Oracle Database container image for 'ee' version 19.3.0 is ready to be extended:

    --> oracle/database:19.3.0-ee

  Build completed in 905 seconds.
```

실행 멸령어의 Option들은 이 [링크](https://github.com/oracle/docker-images/tree/main/OracleDatabase/SingleInstance)를 참조하자. 그리고 데이터 저장을 위해서 Volume(-v)을 위한 Folder를 생성했다.

```
docker run --name oracle19c \
-p 1521:1521 -p 5500:5500 \
-e ORACLE_PWD=Oracle1234 \
-e ORACLE_EDITION=standard \
-e ENABLE_ARCHIVELOG=true \
-v /D/docker-images/OracleDatabase/SingleInstance/dockerfiles/19.3.0/data/:/opt/oracle/oradata \
oracle/database:19.3.0-ee
```

# SQLDEVELOPER

아래 Query로 SQLDeveloper에서 19c로 접속이 가능한지 확인해보자. 아래와 같이 접속정보를 입력 후 SQL 워크시트를 열어보자.

![image](https://user-images.githubusercontent.com/22446581/118417485-3fdfc280-b6ef-11eb-906b-2fd2d19ac220.png)

아래 쿼리로 실행하는 DB Version을 확인한다.

```sql
select Banner
  from v$version
  
BANNER
----------------------------------------------------------------------
Oracle Database 19c Standard Edition 2 Release 19.0.0.0.0 - Production
```



# 참고


  * https://github.com/oracle/docker-images/tree/main/OracleDatabase/SingleInstance
  * https://www.oracle.com/database/technologies/faq-jdbc.html

