---
published: false
layout: single
title: "[Kotlin] gradle로 kotlin script 실행."
comments: true
categories:
  - java
tags:
  - [java, kotlin, grade]
date: 2021-08-25
last_modified_at: 2021-08-25
toc: true
toc_sticky: true
---
Gradle로 Kotlin을 Script 방식으로 실행.



* 결론
  * Kotlin 과 Gradle 로 `kt` 형식의 파일을 실행.
* 현상
  * 요즘 Kotlin에 빠져서 공부하고 있다. Java 도 좋은데 Kotlin 이 생산성이 좋다는 풍문 때문에 공부중이다.
  * 학습 중 kotlin 을 Shell 처럼 script 방식으로 실행할 수 있다는 점을 확인했다.
    * 이 기능을 이용해서 서버 작업 Script로 응용하면 좋을 것은 생각이 들었다.
      * 이게 삽질의 시작이다.
* 원하는 방식
  * Gradle 의 종속성 관리를 사용하면서 Kotlin 을 실행하고 싶다.
* 찾는 방식
  * Gradle로 Kotlin Script 를 실행.
    * 실패...누가 알면 알려줬으면 좋겠다.
  * Gradle 로 Kotlin File 실행.
* 설정 방법
  * Gradle Task 를 Setup.




* 아직 `kts` 를 실행하는 방법은 찾지 못함.




* kotlin script를 gradle 로 시작하기.
* https://docs.gradle.org/current/userguide/tutorial_using_tasks.html
  * -q 의미
    * 뭐야....그냥 로그를 숨기는 명령어잖아...
* https://www.baeldung.com/gradle-command-line-arguments
  * gradle run with argument
* https://kotlinexpertise.com/execute-kotlin-scripts-with-gradle/

  * 
