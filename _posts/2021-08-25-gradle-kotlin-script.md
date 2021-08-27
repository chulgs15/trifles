---
published: true
layout: single
classes: wide
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

[그림 필요]

# 결론
* Kotlin 과 Gradle 로 `kt` 형식의 파일을 실행.

* 소스는 [github](https://github.com/chulgs15/sinabro/tree/master/gradle-kotlin-script) 에서 확인 할 수 있다.

## Idea
요즘 Kotlin이 생산성이 좋다는 풍문에 혹해서,  Kotlin 이 재밌어서 공부하고 있다. 학습 중에 Kotlin을 Shell Script 처럼 서버를 관리하는 언어로서 가능성을 시험해보고 싶었다. 

### Kotlin Script(실패)
Kotlin은 `kts` 파일형식의 Script를 가진다. Gradle 의 Library 를 참조할 수 있었지만, 문제는 실행이다. Gradle 로 `kts` 를 실행할 수 없었다. (고수님들이 알려주시면 감사겠습니다.) 

Intellij 에서 `kts`프로그램을 실행 명령어를 보니 모든 Jar를 classpath에 설정해서 실행하고 있다.

```
C:\jdk11\bin\java.exe ^
-classpath C:\kotlin-compiler-1.5.21\kotlinc\lib\kotlin-compiler.jar;^
C:\kotlin-compiler-1.5.21\kotlinc\lib\kotlin-reflect.jar;^
C:\kotlin-compiler-1.5.21\kotlinc\lib\kotlin-stdlib.jar;^
C:\kotlin-compiler-1.5.21\kotlinc\lib\kotlin-script-runtime.jar org.jetbrains.kotlin.cli.jvm.K2JVMCompiler ^
-cp C:\kotlin-compiler-1.5.21\kotlinc\lib\jvm-abi-gen.jar;^
C:\kotlin-compiler-1.5.21\kotlinc\lib\kotlin-test.jar;^
C:\kotlin-compiler-1.5.21\kotlinc\lib\kotlin-daemon.jar;^
....
C:\kotlin-compiler-1.5.21\kotlinc\lib\slf4j-api-1.7.32.jar^
 -kotlin-home C:\kotlin-compiler-1.5.21\kotlinc ^
-script %1 
```

위 명령어를 Window Batch로 개발할까...고민을 하다가 관리하기 어렵다고 생각해서 다른 방법을 생각했다.

### Gradle Task

검색 중, Gradle의 Task를 활용해서 각 `kt` 파일을 실행할 수 있는 방법을 찾았다. Task 는 아래와 같이 Window에서 실행할 수 있다. 
```bash
gradle "task이름"
```
내가 원하는 작업을 task로 각각 지정한 후, 필요한 작업을 선택해서 실행할 수 있다. 

#### Intellij Project 생성

Kotlin DSL build script로 build를 만들었다.

![image](https://user-images.githubusercontent.com/22446581/131075192-bd1442f6-fd0b-45a9-af9d-f409781d6732.png)


#### Task Kotlin 프로그램 생성.

각 작업을 지정하는 Kotlin 파일을 만든다. 구별해서 실행할 수 있도록 Task1,2 로 지정한다.

![image](https://user-images.githubusercontent.com/22446581/131076421-2237f795-747c-4783-a8c8-4155f14f7b56.png)

#### build.gradle.kts

kotlin 에서 `task`를 지정해서 작업을 나눈다. `Task1.kt` 는 `Task1Kt`라는 class 파일를 생성한다. 그래서 main 클래스는 class 파일 이름은 class 파일명으로 지정해야 한다.

그리고 `taskAll` 은 `task1`과 `task2`를 한번에 실행할 수 있도록 묶었다.

```kotlin
plugins {
    kotlin("jvm") version "1.5.21"
}

group = "org.example"
version = "1.0-SNAPSHOT"

repositories {
    mavenCentral()
}

dependencies {
    implementation(kotlin("stdlib"))
}

task<JavaExec>("task1") {
    classpath = sourceSets.getAt("main").runtimeClasspath
    main = "org.example.tasks.Task1Kt"
}

task<JavaExec>("task2") {
    classpath = sourceSets.getAt("main").runtimeClasspath
    main = "org.example.tasks.Task2Kt"
}

task("taskAll") {
    dependsOn("task1")
    dependsOn("task2")
}
```
#### 결과

정의한 3가지 Task를 실행한 결과다.

##### task 1 의 결과.
```bash
D:\github\sinabro\gradle-kotlin-script>gradlew "task1"
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true

> Task :task1
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
hello, it is Task1

BUILD SUCCESSFUL in 4s
2 actionable tasks: 1 executed, 1 up-to-date
```

##### task 2 의 결과.
```bash
D:\github\sinabro\gradle-kotlin-script>gradlew "task2"
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true

> Task :task2
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
Hello, it is Task2.

BUILD SUCCESSFUL in 4s
2 actionable tasks: 1 executed, 1 up-to-date
```

##### taskAll 의 결과.
```bash
D:\github\sinabro\gradle-kotlin-script>gradlew "taskAll"
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true

> Task :task1
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
hello, it is Task1

> Task :task2
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
Hello, it is Task2.

BUILD SUCCESSFUL in 4s
3 actionable tasks: 2 executed, 1 up-to-date
```

## Dynamic하게 kt 파일 선택하기.

`kotlin.build.kts` 파일에서 mainClass 를 지정할 수 있다면, 실행하려는 `kt`도 arguement로 지정할 수 없을까?

### build.gradle.kts
```kotlin
...

task<JavaExec>("taskWithName") {
    classpath = sourceSets.getAt("main").runtimeClasspath
    if (project.hasProperty("args")) {
        val mainClassName: String = "org.example.tasks." + (project.property("args") as String) + "Kt"
        println("Main Class Name : ${mainClassName}")
        main = mainClassName
    } else {
        println("No args Error")
    }
}
```

#### 결과

`-Dargs` 는 Project 에서 사용할 arguement 를 정의할 수 있다. 아래 실행결과를 보면 `Task1.kt`의 실행을 확인할 수 있다.

```kotlin
D:\github\sinabro\gradle-kotlin-script>gradlew taskWithName -Pargs="Task1"
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true

> Configure project :
Main Class Name : org.example.tasks.Task1Kt

> Task :taskWithName
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
hello, it is Task1

BUILD SUCCESSFUL in 4s
2 actionable tasks: 1 executed, 1 up-to-date
D:\github\sinabro\gradle-kotlin-script>
```
## kt 에 argument 전달하기.

자, 이제  `main(args: Array<String>)` 에서 `args` 에게 우리가 지정한 값을 전달 해보자.  arguement 확인을 위한 kotlin file `TaskWithArguements.kt` 를 만들어 보자. 

```kotlin
package org.example.tasks

fun main(args: Array<String>) {
    args.forEach {
        println(it)
    }
}
```

실행은 기존 `taskWithName` Task를 사용하자. `main` fun 에 arguements를 전달하기 위해 `--args` 옵션을 사용하자.

```bash
D:\github\sinabro\gradle-kotlin-script>gradlew taskWithName -Pargs="TaskWithArguements" --args="hello wolrd"
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true

> Configure project :
Main Class Name : org.example.tasks.TaskWithArguementsKt

> Task :taskWithName
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
hello
wolrd

BUILD SUCCESSFUL in 4s
2 actionable tasks: 1 executed, 1 up-to-date

```

# 참고

* https://docs.gradle.org/current/userguide/tutorial_using_tasks.html
  * -q 의미
* https://www.baeldung.com/gradle-command-line-arguments
  * gradle run with argument
* https://kotlinexpertise.com/execute-kotlin-scripts-with-gradle/
    * 참고했지만, 활용하지 못함.
* https://docs.gradle.org/current/userguide/more_about_tasks.html
    * Task 사용방법.
