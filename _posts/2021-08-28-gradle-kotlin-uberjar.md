---
published: true
layout: single
title: "[Kotlin] gradle 로 executable jar 만들기."
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
Kotlin 도 executable jar 를 만들 수 있을까?

![logo](https://user-images.githubusercontent.com/22446581/131240883-42896779-4240-4ffe-bf48-16ac3dd8742e.png)

# 결론
* Gradle 로 fat or uber jar 를 만든다.

내 PC의 Script 작업들은 대부분 Java 로 Executable Jar 로 만들었다. Batch 나 Shell 문법이...생각 이상으로 어려워 생성과 유지보수에 많은 시간이 필요했다. 이런 고민을 덜기 위해, Java 로 만든 Executable Jar 명령어를 alias 로 만들어 서버에서 실행하고 있다.

그런데 Kotlin 이 문든 배우고 싶고, 기존 Jar 파일들도 Renewal 이 필요해서 갈아 엎기로 했다. 그러면 가장 먼저 확인해야 할 것은 Kotlin 도 Executable Jar 를 만들 수 있을까?

## First attempt

일단 인터넷에서 찾아본 결과, 대표로 3가지 방법이 있다.

1. Intellij  에서 Executable Jar 만들기.
2. Shadow Jar plug-in.
3. Gradle executable jar.

Java 는 별 수고로움 없이 한 것 같은데... Kotlin 은 방법이 여러개로 나뉘는 것 같다. 일단 1번 방법은 Library 들이 포함하지 않았다. Jar 파일을 실행할 때, Classpath option 에 사용한 종속성을 모두 넣어야 했다.

2번 방법은...그냥 마음에 안들었다. Jar  파일을 만드는데 왜 Plugin 을 추가하면 뭔가 사족같다는 느낌이 들었다. 그리고 plug-in 을 사용할 줄 몰랐다...ㅠ

3번 방법을 찾아보면 아래와 같은 build script 를 많이 볼 수 있는데...

```kotlin
jar {
    manifest {
        attributes 'Main-Class': 'org.test.MainKt'
    }
    archiveName 'test.jar'
    from { configurations.compile.collect { it.isDirectory() ? it : zipTree(it) } }
}
```
문제는 `it.isDirectory()` 에서 오류가 발생했다. 현재 6.8 버젼에서는 지원하지 않는 것 같다. 결국 공식 문서를 찾아보기로 했다. 

## Gradle Uber or Fat jar

공식문서에서는 `uber or fat jar` 라는 명칭으로 지원하고 있다. (uber 라는 이름의 기원을 알면...공유부탁드립니다.) 예제를 만들어보자.

### Kotlin project 생성.

kotlin DSL build script 를 선택하자.

![image](https://user-images.githubusercontent.com/22446581/131196179-982dc7ad-6678-4801-8813-2f7492bfb083.png)

### build.gradle.kts

테스트를 위해, `common-io` 종속성을 추가했다. `common-io`의 예제가  `main-class`에서 정상 실행여부를 확인한다.

다음은 추가한  `task` 다.  `mainifest` 영역에서 실행할 Kotlin 파일의 이름을 설정한다. 다른 부분은 Gradle 의 공식문서의 예제를 참고했다.

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
    implementation("commons-io:commons-io:2.6")
}


tasks.register<Jar>("uberJar") {
    archiveClassifier.set("uber")
    from(sourceSets.main.get().output)
    dependsOn(configurations.runtimeClasspath)
    from({
        configurations.runtimeClasspath.get().filter { it.name.endsWith("jar") }.map { zipTree(it) }
    })

    manifest {
        attributes["Implementation-Title"] = "Gradle Jar File Example"
        attributes["Implementation-Version"] = version
        attributes["Main-Class"] = "org.example.test.HelloWorldKt"
    }
}
```

### HelloWorld.kt

`FilenameUtils.normalize`은 이름을 정규화한다. `..`이 붙으면 이전 폴더로 돌아가기 때문에 결과는 ` c:/project.txt`가 나타날 것이다.

```kotlin
package org.example.test

import org.apache.commons.io.FilenameUtils

fun main(args: Array<String>) {
    val filename = "c:/lang/../project.txt"
    val normailzed = FilenameUtils.normalize(filename)
    println(normailzed)
}
```

### 결과

```bash
PS D:\> java -jar D:/github/sinabro/kotlin-uber-jar/build/libs/kotlin-uber-jar-1.0-SNAPSHOT-uber.jar
c:\project.txt
```

# 참고

* https://www.baeldung.com/gradle-fat-jar
  * fat jar  만들기 
* https://blog.leocat.kr/notes/2017/10/11/gradle-shadowjar-make-fat-jar
  * shadow jar 만들기.
* https://docs.gradle.org/current/userguide/working_with_files.html#sec:creating_uber_jar_example
  * Gradle uber jar  만들기.
* http://www.gisdeveloper.co.kr/?p=1744
  * common-io 예제
* https://stackoverflow.com/questions/41794914/how-to-create-the-fat-jar-with-gradle-kotlin-script
  * manifest 설정.

