---
published: true
layout: single
title: "Clean Code 제 8장. 단위 테스트"
comments: true
categories:
  - book
tags:
  - [java, cleancode]
date: 2021-04-17
last_modified_at: 2021-04-17
toc: true
toc_sticky: true
---
 테스트 케이스에 대한 이야기.

<img src="https://woowabros.github.io/img/2019-03-20/book.jpg" alt="test" style="zoom: 10%;" />

## 0. 주관적인 결론

* 테스트 코드도 깨끗한 코드를 유지하자.



## 1. 테스트 케이스의 존재이유.

 테스트 케이스가 있으면 변경이 두렵지않다!! 테스트 케이스가 없으면 모든 변경이 잠정적인 버그다.

## 2. TDD 의 법칙.

1. 첫째 법칙 : 실패하는 단위 테스트 작성할 때까지 실제 코드를 작성하지 않는다.
2. 둘째 법칙 : 컴파일은 실패하지 않으면서 실행이 실해하는 정도로만 단위 테스트를 작성한다.
3. 셋째 법칙: 현재 실패하는 테스트를 통화할 정도로만 실제 코드를 작성한다.

## 3. 이중 표준.

 테스트 케이스 코드는 실제 코드 처럼 효율적일 필요 없다. 테스트 케이스를 위한 Rule 을 만들자. 이 Rule 은 테스트 케이스 코드의 표현력을 높이고 간결하게 작성하는 것을 안내해야 한다.