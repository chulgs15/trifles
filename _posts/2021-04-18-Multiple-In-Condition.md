---
published: true
layout: single
title: "[SQL] Multi Case Condition"
comments: true
classes: wide
categories:
  - Database
tags:
  - [sql]
date: 2021-04-20
last_modified_at: 2021-04-20
toc: true
toc_sticky: true
---
Case 문장을 사용해서 여러 조건을 바꿔서 사용하는 방법.

# Multi Case Condition

간혹 요청에 따라 조건을 바꿔야 하는 경우가 있따. 문자열을 합쳐 Dynamic SQL을 만드는 방법이 있지만, Static SQL로 해결하는 방법이 있다. 

```
select *
  from dba_users du
 where 1=1
   and CASE 
     WHEN du.account_status = 'OPEN' THEN
      1
     WHEN du.account_status in ('LOCKED', 'EXPIRED ' || '&' || ' LOCKED') THEN
      2
     ELSE
      3
     END  = &num
 ;
```

위 쿼리에서 'num'을 조작하면 바뀐 결과가 나타난다.
