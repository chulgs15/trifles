---
published: true
layout: single
title: "[Oracle] Child cursor"
comments: true
categories:
  - database
tags:
  - [oracle, database]
date: 2022-05-10
last_modified_at: 2022-05-10
toc: true
toc_sticky: true
---
SQL 의 Child Cursor 에 대한 이야기.

![제목](https://user-images.githubusercontent.com/22446581/168464200-23afeba8-be98-4ed2-9541-bdf5b6b74332.JPG)

# Version

* OS : Oracle Linux Server
* DB : Oracle 19.10.0.0

# 결론

* Oracle 은 같은 SQL 이라도 다양한 이유로 인해 다른 SQL 로 실행할 수 있다.
* 파라미터`_cursor_obsolete_threshold` 로  version_count 의 최대값을 설정할 수 있다.

# 현상

AWR Report 에서 Version Count 가 높은 SQL 이 존재하는 것을 발견했다. Version Count 에 관련되서 정리해보려고 한다.

오라클은 동일한 SQL 문장을 가져도 같은 SQL 으로 인식하지 않는 경우가 있다. 

> A statement with the same syntax but different semantics uses a child cursor.

## BIND_MISMATCH

이유는 여러가지가 있는데, 검색하면 심심치 않게 보이는 것이 `BIND_MISMATCH` 다.  SQL Bind 값의 변화로 Bind SQL 을 공유할 수 없는 현상이다. 아래와 같은 예제를 볼 수 있다.  같은 SQL 이지만 Bind 값 Type 의 변화로 Child Cusror 가 만들어졌다. `v$sql_shared_cursor` view 에서 변화한 이유를 확인할 수 있다.

```sql
declare
  p_invoice_num1 varchar2(32) := 'aaaaaaaaaaaaaaaaaaaaaaaa';
  p_invoice_num2 char(4000) := 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa';
  l_cnt number;
begin
  select count(1) 
    into l_cnt 
    from ap_invoices_all aia2 
   where aia2.invoice_num = p_invoice_num1;
   
  select count(1) 
    into l_cnt 
    from ap_invoices_all aia2
   where aia2.invoice_num = p_invoice_num2;   
end;
/

-- 확인 SQL
SELECT ssc.sql_id,
       ssc.child_number,
       ssc.bind_mismatch
  FROM v$sql_shared_cursor ssc
 WHERE 1 = 1
   AND ssc.sql_id IN (SELECT sq.sql_id
                        FROM v$sql sq
                       WHERE 1 = 1
                         AND sq.sql_text LIKE 'SELECT%AP_INVOICES_ALL%AIA2%');
```

## BIND_LENGTH_UPGRADEABLE

Bind 의 길이의 변화로도 Child Cursor 가 만들어 질 수 있다. 확인할 수 있는 Column 은 `BIND_LENGTH_UPGRADEABLE` 이다.

```sql
declare
  p_invoice_num1 varchar2(32) := 'aaaaaaaaaaaaaaaaaaaaaaaa';
  p_invoice_num2 varchar2(4000) := 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa';
  l_cnt number;
begin
  select count(1) 
    into l_cnt 
    from ap_invoices_all aia3
   where aia3.invoice_num = p_invoice_num1;
   
  select count(1) 
    into l_cnt 
    from ap_invoices_all aia3
   where aia3.invoice_num = p_invoice_num2;   
end;
/

-- 확인 SQL
SELECT ssc.sql_id,
       ssc.child_number,
       ssc.bind_length_upgradeable
  FROM v$sql_shared_cursor ssc
 WHERE 1 = 1
   AND ssc.sql_id IN (SELECT sq.sql_id
                        FROM v$sql sq
                       WHERE 1 = 1
                         AND sq.sql_text LIKE 'SELECT%AP_INVOICES_ALL%AIA3%');
```

## HASH_MATCH_FAILED

`HASH_MATCH_FAILED`는 FROM 절의 테이블에 변화가 발생(CREATE INDEX, DROP INDEX ... 등) 이 발생할 때 생길 수 있다.  

 예제는 처음 실행했던 SQL 실행계획에 변화를 만들었다. `AP_INVOICES_N6` Index 를 Drop 했다. `AP_INVOICES_N6`는 `AP_INVOICES_ALL.INVOICE_NUM` 컬럼을 가진 Index 다. `INVOICE_NUM`  컬럼은 예제서 조건절로 사용하고 있어 Index 를 활용한다. 하지만 Index 가 사라졌으니 Full Table Scan 혹은 다른 Index 를 활용할 것이다. 그러면 기존 실행계획은 더 이상 사용할 수 없기 때문에 Child Cursor 가 생기게 된다. 

```sql
-- 처음 실행.
declare
  p_invoice_num ap.ap_invoices_all.invoice_num%type;
  l_cnt number;
begin
  select count(1) 
    into l_cnt 
    from ap_invoices_all aia5
   where aia5.invoice_num = p_invoice_num;
end;
/

-- 인덱스 Drop
drop index ap.AP_INVOICES_N6;

-- Child SQL 확인.
SELECT ssc.sql_id,
       ssc.child_number,
       ssc.hash_match_failed
  FROM v$sql_shared_cursor ssc
 WHERE 1 = 1
   AND ssc.sql_id IN (SELECT sq.sql_id
                        FROM v$sql sq
                       WHERE 1 = 1
                         AND sq.sql_text LIKE 'SELECT%AP_INVOICES_ALL%AIA5%');
```

# Version Count 

위 글에서 전부는 아니지만 SQL  에서 Version Count 가 발생하는 이유를 확인했다. VERSION_COUNT  가 발생하면 무슨 문제가 발생할 것인가? 아래 표처럼, 같은 SQL 이지만  메모리가 2배로 발생했다. 같은 SQL 이지만 여러 Case 의 Version 을 하나의 Linked List 처럼 계속해서 가져가기 때문에 메모리 더 많이 가져간다. 

| SQL_ID        | SHARABLE_MEM |
| ------------- | ------------ |
| 4fwsf50y65akn | 28759        |
| 4fwsf50y65akn | 36887        |

이렇게 발생하는 VERSION_COUNT 를 제어할 수 있는 파라미터가 있다.  아래 SQL 결과 VERSION_COUNT 를 8192 개 까지 생성할 수 있도록 설정되어 있다. 

```sql
SQL> SELECT ksppinm,
       ksppstvl
  FROM x$ksppi  x,
       x$ksppcv y
 WHERE x.indx = y.indx
   AND x.ksppinm LIKE '_cursor_obsolete_threshold'
;

KSPPINM
--------------------------------------------------------------------------------
KSPPSTVL
--------------------------------------------------------------------------------
_cursor_obsolete_threshold
8192

```



# 추가확인.

한가지 궁금증이 생겼다. 이 [링크](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=rnarnsms73&logNo=40152907186)를 보면 `BIND_MISMATCH`로 Child Cursor 를 만들지 않는 방법이 있다. 확인할 겸 한번 테스트 해보았다. 수행 결과, Child Cursor 가 생기지 않은 것을 확인했다.

```sql
declare
  p_invoice_num1 varchar2(32) := 'aaaaaaaaaaaaaaaaaaaaaaaa';
  p_invoice_num2 varchar2(4000) := 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa';
  l_invoice_num  varchar2(4000);
  l_cnt number;
begin
  l_invoice_num := rpad(p_invoice_num1, 4000, ' ');
  
  select count(1)
    into l_cnt
    from ap_invoices_all aia4
   where 1=1
     and aia4.invoice_num = trim(l_invoice_num);
     
  l_invoice_num := rpad(p_invoice_num2, 4000, ' ');
  
  select count(1)
    into l_cnt
    from ap_invoices_all aia4
   where 1=1
     and aia4.invoice_num = trim(l_invoice_num);     
end;
/

-- 확인 SQL
select sq.*
  from v$sql sq
where 1=1
  and sq.sql_text like 'SELECT%AP_INVOICES_ALL%AIA4%';
```

# 참고

* https://mikedietrichde.com/2018/09/11/oracle-12-2-and-higher-set-_cursor_obsolete_threshold-to-old-default/
* https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=rnarnsms73&logNo=40152907186
* Troubleshooting: High Version Count Issues (Doc ID 296377.1)
