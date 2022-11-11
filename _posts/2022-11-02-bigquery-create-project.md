---
published: false
layout: single
title: "[bigquery] Dataset"
comments: true
categories:
  - bigquery
tags:
  - [bigquery, google]
date: 2022-11-02
last_modified_at: 2022-11-12
toc: true
toc_sticky: true
---
Bigquery 에서 Project 생성.

![Google Cloud to Bigquery](https://user-images.githubusercontent.com/22446581/199433625-15460fb6-56d5-49ef-99e8-233c2a5f97e0.jpg)

# 결론

* Dataset 을 만들어보자.

# Dataset 생성.

## 정의

* 테이블과 뷰를 생성할 수 있는 컨테이너다.
  * 컨테이너란 어떤 환경에서나 실행하기 위한 필요한 모든 요소를 포함하는 소프트웨어 패키지다.
  * 즉 Dataset 없이 테이블과 뷰를 생성할 수 없다.

* 개인은 프로젝트는 계정을 만들 때 같이 최소 1개가 생성된다.

## 제한 사항

  * 프로젝트 마다 데이터 세트 이름은 달라야 한다.
  * Dataset 을 생성할 때, Region 을 생성한다. 한번 결정한 Region 은 변경할 수 없다.
  * 쿼리에서 참조하는 모든 테이블은 같은 위치의 데이터 세트에 저장해야 한다. 
    * 쿼리 내에서는 다른 리젼의 데이터를 조회할 수 없다.

  * 테이블을 복사할 때 소스 테이블과 대상 테이블을 포함하는 데이터 세트는 같은 위치에 있어야 한다.
    * 다른 리젼끼리는 Table 복사가 안된다.

## 생성

### console 에서 editor 로 생성.

```sql
create schema mydataset
options (
);
```

#### 생성 결과



### client library 로 생성

```kotlin
package org.test.consoleApp

import com.google.cloud.bigquery.*

fun main() {
    println("hello world")

    val datasetName = "helloworld_test_221112"

    val bigquery = BigQueryOptions.getDefaultInstance().service

    val datasetInfo = DatasetInfo.newBuilder(datasetName)
        .setDescription("this is first dataset by java")
        .build()

    val newDataset = bigquery.create(datasetInfo)
    val newDatasetName = newDataset.datasetId.dataset

    println("New Dataset Name is " + newDatasetName)
}

```

#### 생성 결과

