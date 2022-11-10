---
published: true
layout: single
title: "[bigquery] Google Cloud to bigquery"
comments: true
categories:
  - bigquery
tags:
  - [bigquery, google]
date: 2022-11-01
last_modified_at: 2022-11-01
toc: true
toc_sticky: true
---
From Google Cloud to Bigquery

![Google Cloud to Bigquery](https://user-images.githubusercontent.com/22446581/199433625-15460fb6-56d5-49ef-99e8-233c2a5f97e0.jpg)

# 결론

* bigquery 를 구성하기 위한 구성 Hierachy 를 알아보자.

# Google Cloud Hierachy

![bigquery-hierachy](https://user-images.githubusercontent.com/22446581/199432449-51b078fc-b9a9-46ce-8c16-911e76b3a6df.png)

## Organization

* 최상위(Root) Node 로 회사를 대표하는 객체다.
* Google Workspace 나 Cloud ID 는 Organization 1개를 만들 수 있다.
* Organization Level 에서 정책을 수립 시 하위 Node 도 같이 적용한다.
* Organization Level 에서 Slot 을 구매하고 할당한다.

## Folder

* 프로젝트를 Grouping 하는 단위
* 조직내에 법인, 부서, 팀 단위로 생성 및 관리.

## Project

* 서비스 생성, 사용, 결제를 위한 단위
* 할당량/한도를 설정할 수 있다.

## Region

* 특정 지리적인 장소 (예: 서울)
* Multi region : 두개 이상의 Region 을 가진 지형
  * Ex. US, EU
* Dataset 을 만들 때 지정한다. 수정은 불가
  * 대신 다른 위치로 이동, 복사 가능.

## Dataset

* DB Schema 정보 (Table, Object...) 를 가진 논리적인 Container
* Dataset 에 대한 CRUD 로 요금을 청구하지 않는다.
* 엑세스 제어, 데이터 암호화 적용 가능.





























