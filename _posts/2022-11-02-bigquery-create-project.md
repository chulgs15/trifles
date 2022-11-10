---
published: false
layout: single
title: "[bigquery] Project 생성."
comments: true
categories:
  - bigquery
tags:
  - [bigquery, google]
date: 2022-11-02
last_modified_at: 2022-11-02
toc: true
toc_sticky: true
---
Bigquery 에서 Project 생성.

![Google Cloud to Bigquery](https://user-images.githubusercontent.com/22446581/199433625-15460fb6-56d5-49ef-99e8-233c2a5f97e0.jpg)

# 결론

* 프로젝트를 만들어보자. 그래야 Dataset 을 만들 수 있다.
* public dataset 을 만드는 구문을 생성하자

# Dataset 생성.

* 정의 : 

  * 테이블과 뷰를 생성할 수 있는 컨테이너다.
    * 컨테이너란 어떤 환경에서나 실행하기 위한 필요한 모든 요소를 포함하는 소프트웨어 패키지다.
    * 즉 Dataset 없이 테이블과 뷰를 생성할 수 없다.

  * 개인은 프로젝트는 계정을 만들 때 같이 최소 1개가 생성된다.

* 제한 사항

  * Dataset 을 생성할 때, Region 을 생성한다. 한번 결정한 Region 은 변경할 수 없다.
  * 쿼리에서 참조하는 모든 테이블은 같은 위치의 데이터 세트에 저장해야 한다. ??
    * 내가 다른 리젼의 데이터를 조회할 수 없다.

  * 테이블을 복사할 때 소스 테이블과 대상 테이블을 포함하는 데이터 세트는 같은 위치에 있어야 한다.
    * 다른 리젼끼리는 Table 복사가 안된다.

  * 프로젝트 마다 데이터 세트 이름은 달라야 한다.

* 생성

  * console 에서 editor 로 생성.

    ```sql
    create schema mydataset
    options (
    );
    ```

  * client library java 로 생성

    ```
    이건 한번 해보자.
    ```




# Table

* 정의

  * 데이터를 보관하는 Object 로, 열 기반으로 저장한다.
  * 스키마 : 모든 테이블의 열 이름, 데이터 유형 같은 메타 정보.

* 이름

  * `projectname.datasetname.tablename` 형식

* 유형

  * 테이블 : 
  * 외부 테이블 : 
  * 뷰 : 

* 제한사항

  * 데이터 세트마다 이름이 고유해야 한다.
  * 테이블 복사는 동일한 Region 에서만 가능하다.
    * US => EU 로 복사 불가능하다.
  * 여러 소스 테이블로 대상 테이블에 복사할 때, 소스 테이블들은 스키마가 동일해야 한다.
    * :arrow_double_down: 열 길이가 다르다면 복제가 가능한가?
  * API 호출을 사용할 때 데이터 세트의 테이블 수가 5만개에 근접하면 성능이 저하됩니다.
  * 

  



* 
* public data 를 개인 Dataset 으로 가져오려면 어떻게 해야 하는가?
  * 복제 기능이 있네.
* 클러스터 테이블 
  * 정의
  * 특징
    * 비용 보장하지 않는다.
    * 파티션을 위한 소량의 데이터를 생산
    * 프루닝
    * 파티션과 클러스터 둘다 적용이 가능하다.

* 데이터 샤딩
  * prefix 사용
  * partition 을 더 추천

* 열모드
  * 테이블 스키마에 중첩 및 반복 열 지정
  * record type 으로 설정 후 struct 명령어로 연결한다.

* 파티션 테이블
  * 테이블의 모든 쿼리에 partition 컬럼의 조건을 추가하는 option












