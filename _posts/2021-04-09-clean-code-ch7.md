---
published: true
layout: single
title: "Clean Code 제 7장. 경계"
comments: true
categories:
  - book
tags:
  - [java, cleancode]
date: 2021-04-10
last_modified_at: 2021-04-10
toc: true
toc_sticky: true
---
외부 API, Library, Framework와 구현 기능의 경계와 통합에 대한 이야기.

<img src="https://woowabros.github.io/img/2019-03-20/book.jpg" alt="test" style="zoom: 10%;" />

## 0. 주관적인 결론

* 외부 API와 구현 기능의 경계를 명확히 구분하자.
* 학습 테스트하자. 두번 하자.

## 1. 인터페이스 제공자/사용자

인터페이스 제공자와 사용자는 필요한 관점이 다르다. 제공자의 의도와 사용자의 요구가 잘 맞을을 수도 있지만, 그렇지 않다면 문제의 소지가 있다. 또한 사용자는 외부 코드를 배우기도, 통합하기도 어렵다. 

## 2. 학습 테스트

학습 테스트는 테스트 케이스를 외부 코드를 학습하기 위해 작성하는 행동이다. 장점은...

1. 학습 테스트는 공짜 이상이다.
2. 학습 테스트는 패키지가 예상대로 도는지 검증한다.
3. 실제 코드와 동일한 방식으로 인터페이스를 사용하는 테스트 케이스는 언젠가 만들어야 한다.

## 3. 먼저 만들기

아직 외부 API에 대해 잘 모른다면, API의 기능을 정의하고 정의에 맞는 객체를 적절한 패턴으로 먼저 구현하자. 책은 Adapter 패턴을 예시로 들었다. 예상한 기능을 새로운 Class로 감싸 원하는 기능을 제공하는 인터페이스로 변환했다.
