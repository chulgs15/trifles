---
published: true
layout: single
title: "[Database] Oracle SQLDeveloper 성능 최적화"
comments: true
categories:
  - database
tags:
  - [serious, database, oracle]
date: 2021-04-29
last_modified_at: 2021-04-29
toc: true
toc_sticky: true
---
 SQLDeveloper 최적화 방법과 시작 메뉴 검색방법.

# Oracle SQLDeveloper More Faster~!!

[<img src="https://upload.wikimedia.org/wikipedia/en/thumb/6/68/Oracle_SQL_Developer_logo.svg/1200px-Oracle_SQL_Developer_logo.svg.png" alt="img" style="zoom: 25%; display: block; text-align: center;" />](https://upload.wikimedia.org/wikipedia/en/thumb/6/68/Oracle_SQL_Developer_logo.svg/1200px-Oracle_SQL_Developer_logo.svg.png) 

 오라클에서 무료로 제공하는 DB 관리와 SQL 쿼리 기능을 가진 Tool이다. 좋은 Tool인데 처음 실행이 너무 느리다. 몇년간 봐왔지만 개선이 잘 안되는 것 같다. 이번 Post에서 성능 향상 방법 3가지와 도움되는 설정 2가지를 이야기한다.

## 시작시간 단축

### 1. Extension 끄기

 최초 SQLDeveloper는 모든 기능이 활성화 되어있다. SQL 개발만 설정하기 위해서는 '도구 > 기능' 메뉴로 들어가 기능을 비활성화 시키자

![image](https://user-images.githubusercontent.com/22446581/116514457-0133c500-a906-11eb-9c0d-59d62711c231.png)

 Database 이전과 버젼제어 기능을 해지한다. Database 이전은 DB Vendor별 Migration을 지원한다. 버젼제어는 Git과 Subversion을 지원한다.

![image](https://user-images.githubusercontent.com/22446581/116514491-0f81e100-a906-11eb-9c3a-3efe945c08aa.png)

 그리고 데이터베이스 옵션에서는 'Oracle OLAP'만 설정하자. 그러면 SQL 개발을 위한 기능들만 자동으로 설정된다.

![image](https://user-images.githubusercontent.com/22446581/116514602-39d39e80-a906-11eb-826d-2c73eb768f58.png)

### 2. IndexPreferencesTask 기능 Off.

 이 기능이 성능하향의 주범(?)이다. 경험 상, 이 기능이 없어서 문제된 적이 없으니 사용하지 말자. 기능을 사용하지 않기 위해서는 실행 Option에 추가해야 한다. 경로는 아래와 같다.

```
sqldeveloper폴더\sqldeveloper\bin\sqldeveloper.conf
```

 파일 내부에 아래 Option을 추가하자.

```
AddVMOption -DIndexedPreferencesCommand=false
```

### 3. Window 테마로 변경

 이론상 SQLDeveloper의 'Oracle' 테마는 '화면실행 > OS에 맞는 화면구성 > Oracle 테마 덧씌우기'라고 한다. 이 때, OS의 테마를 사용하면 Oracle 테마까지 실행하지 않아 실행속도가 빨라진다. 나 같이 느린 PC를 쓰는 사람에게는 조금은 체감이 되지만, 빠른 PC에서는 많이 좋아지는 것을 느낄 수 없으니 선택사항으로 생각하자. 

![image](https://user-images.githubusercontent.com/22446581/116514659-4a841480-a906-11eb-9935-40725b43a6f2.png)

## 쓰기 쉬운 설정.

### Windows 10 시작 메뉴로 등록하는 방법.

 개인적으로 Windows 10의 검색기능이 좋아져서 프로그램을 검색으로 실행하는 편이다. 검색으로 실행하려면 Installshield나 MS Store에서 설치해야 검색의 '앱' 메뉴에서 검색이 가능하다. 편법으로 시작 메뉴에 추가하면 '앱' 메뉴에서 검색할 수 있다.

```
C:\Users\사용자이름\AppData\Roaming\Microsoft\Windows\Start Menu
```

 이 폴더에 SQLDeveloper 바로가기를 추가해보자. 아래와 같이 검색해서 실행이 가능하다.

![image](https://user-images.githubusercontent.com/22446581/116514714-5cfe4e00-a906-11eb-9434-dca6833244ff.png)

### 날짜 형식 변경

 날짜 형식을 'RRRR-MM-DD HH24:MI:SS' 으로 변경하면 Date 컬럼의 데이터 형식이 '2021-03-20 06:36:49' 으로 보일 수 있다.

![image](https://user-images.githubusercontent.com/22446581/116514756-6b4c6a00-a906-11eb-9e69-07e4d7efda7b.png)

## 참고

- https://community.oracle.com/tech/developers/discussion/4479330/sql-developer-20-2-indexpreferencestask-very-slow
- https://m.blog.naver.com/PostView.nhn?blogId=siwoo9999&logNo=221134473727&proxyReferer=https:%2F%2Fwww.google.com%2