---
published: true
layout: single
title: "[Java] Intellij Maven Import 오류."
comments: true
categories:
  - java
tags:
  - [trifles, java, intellij]
date: 2021-07-27
last_modified_at: 2021-07-27
---
Intellij 에서 Maven 프로젝트 등록 시, Language Level 로  발생하는 오류 해결.
# 현상

Intellij에서 Maven 프로젝트를 Import 할때 아래와 같은 오류 메시지가 나타난다. 

> By default, the Java language level is set to 5 which is not supported by the current Java version.

Maven과 Intellij의  Java Compiler 버젼이 서로 달라 발생하는 오류다. 참고로, Maven은 기본적으로 1.5 Version을 사용한다.

# 방법

1. Intellij Setting 에 들어가 Target bytecode version을 JDK 버젼과 맞춘다.

    ![image](https://user-images.githubusercontent.com/22446581/127072075-69c1d2de-1fc2-42ae-bd04-adb96b877738.png)

2. 모듈로 등록한 Project 들의 Language Level 도 JDK 버젼으로 맞춰준다.

    ![image](https://user-images.githubusercontent.com/22446581/125130058-4dd42180-e13b-11eb-8ed6-501f920e31a3.png)

3. Maven 프로젝트를 Reload 한다.

    ![image](https://user-images.githubusercontent.com/22446581/127072147-ba6ef930-40b5-42b7-b224-35dfbaa90450.png)