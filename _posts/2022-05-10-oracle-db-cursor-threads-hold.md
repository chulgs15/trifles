---
published: true
layout: single
title: "[Oracle] Shared Cursor"
comments: true
categories:
  - java
tags:
  - [oracle, database]
date: 2022-05-10
last_modified_at: 2022-05-10
toc: true
toc_sticky: true
---
SQL 의 Child Cursor 에 대한 이야기.

# 결론

* Oracle 은 같은 SQL 이라도 다양한 이유로 인해 다른 SQL 로 실행할 수 있다.
* 파라미터 thread_hold 로  version_count 의 최대값을 설정할 수 있다.

# 현상

AWR Report 에서 Version Count 가 높은 SQL 이 존재하는 것을 발견했다. Version Count 에 관련되서 정리해보려고 한다.

오라클은 동일한 SQL 문장을 가져도 같은 SQL 으로 인식하지 않는 경우가 있다. 

> A statement with the same syntax but different semantics uses a child cursor.

이유는 여러가지가 있는데, 검색하면 심심치 않게 보이는 것이 `BIND_MISMATCH` 다.  SQL Bind 값 길이의 변화로 Bind SQL 을 공유할 수 없는 현상이다. 아래와 같은 예제를 볼 수 있다.



* 현상
    * 오늘 확인했던 SQL 에서 Version Count 가 높은 SQL 을 확인. 
    * 왜 이런 현상이 발생했는가?

* sql cursor 란?
    * Cus

```
A cursor is a name or handle to a specific private SQL area.

you can think of a cursor as a pointer on the client side and as a state on the server side. Because cursors are closely associated with private SQL areas, the terms are 
sometimes used interchangeably.
```

* sql cursor 에 version count 가 생기는 이유

```
 If an identical statement does not exist, then the database allocates a new 
shared SQL area in the shared pool. A statement with the same syntax but 
different semantics uses a child cursor.
```

* bind mismatch

```
**BIND_MISMATCH
The bind metadata does not match the existing child cursor. For example, in the following, the definition of the bind variable 'a' has changed between the 2 statements:

variable a varchar2(100);
select count(*) from emp where ename = :a ->> 1 PARENT, 1 CHILD

variable a varchar2(400);
select count(*) from emp where ename = :a ->> 1 PARENT, 2 CHILDREN
```



* sql cursor 에서 version count 관련된 파라미터

* 그리고 Index 를 Drop 해보자.

* 참고
  * https://mikedietrichde.com/2018/09/11/oracle-12-2-and-higher-set-_cursor_obsolete_threshold-to-old-default/
  * https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=rnarnsms73&logNo=40152907186
  * Troubleshooting: High Version Count Issues (Doc ID 296377.1)
