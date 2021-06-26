---
published: true
layout: single
title: "[Docker] Spring boot를 Weblogic 12c에 이관"
comments: true
categories:
  - java
tags:
  - [trifles, docker, spring, weblogic]
date: 2021-06-27
last_modified_at: 2021-06-27
toc: true
toc_sticky: false
---

 Weblogic으로 Spring boot를 실행해보자.

<img src="https://user-images.githubusercontent.com/22446581/123527957-aad4de00-d71e-11eb-8014-357a1de86f68.PNG" alt="로고" style="zoom:80%;" />

# 결론

* Docker로 만든 Weblogic에 Spring boot를 배포한다.

 [지난 Post](https://chulgs15.github.io/trifles/java/weblogic-docker/)에서 Weblogic을 Docker로 실행했다. 나름 Web App Server라면  Web 프로그램은 만들어야 겠다는 생각이 들었다. 이번 Post는 Spring Boot로 Rest API를 만들어서 배포까지 작업할 예정이다. 소스는 [github](https://github.com/chulgs15/sinabro/tree/master/spring-weblogic)에 있다.

# 개발

1. Spring boot로 Rest API 를 만든다.
2. Weblogic에 자동으로 배포한다.

 만들고 싶은 결과다. Weblogic의 수동배포는 wizard 방식으로  쉽게 할 수 있다. 하지만 build 파일을 계속해서 파일 업로드하는 건 비효율적이라는 생각이 들어 intellij로 자동으로 배포하는 방법을 고민했다.

## Weblogic Deployment

 첫 번째로 생각한 방법은 intellij로 Weblogic을 배포하는 방법을 찾았다. [이 방법](https://blog.seri.la/14)을 시도해 봤는데 문제는 Weblogic Home 이라는 경로가 필요하다. Local 에 설치한 Weblogic은 가능했지만 Docker는 방법을 찾을 수 없었다.

 뭔가 방법이 없을까...Weblogic 도메인 폴더에 이런 폴더가 눈에 띄었다.

> base_domain/autodeploy

 와우...친절하게 readme.txt 가 있다. 

```
This autodeploy directory provides a quick way to deploy applications
to a development server. When the WebLogic Server instance is running
in development mode, applications and modules in this directory are 
automatically deployed.
```

개발 서버는 결과를 빠르게 확인하기 위해 자동 배포 폴더를 제공했다. 가능한 파일 형식은 아래와 같다. 

1. EAR files
2. WAR, EJB JAR, or RAR archive files
3. Exploded archive directories for applications or modules

그래서, 이전 Post 에서 domain 폴더를 Docker `Volume`으로 설정했다. `volume`으로 지정한 폴더는 Host에서도 접근이 가능하다. 

![그림1](https://user-images.githubusercontent.com/22446581/123526673-70feda00-d714-11eb-826c-9d2ca09aec97.png)

생각한 방법은 이렇다. 

1. maven으로 war 파일을 build한다. 
2. weblogic의 autodeploy 폴더에 copy & paste.

## Spring boot

Spring Boot 로 개발과 배포를 위한 순서다.

1. Packaging을 `war`로 설정.
2. `ServletInitializer` 수정
3. `pom.xml` 수정
4. `webapp` 폴더와 `weblogic.xml` 생성.
5. `@RestContrlloer`생성.

### Spring boot 생성 시  Packaging을 war로 설정

jar 파일은 안되는 것인가? 라고 생각할 수 있는데 그렇지 않다. jar 파일로 만들면 war 파일과 같은 구조로 다시 만들어야 하기 때문에 처음부터 war로 packaging하자.(그래도 jar 파일로 하고 싶다면 이 [링크](https://o7planning.org/11901/deploy-spring-boot-application-on-oracle-weblogic-server)를 참조.)

![image](https://user-images.githubusercontent.com/22446581/123527363-c25d9800-d719-11eb-8afe-ec4ef8c92f98.png)



###  ServletInitializer 수정

 `ServletInitializer` class 를 아래와 같이 수정해야 한다. `WebApplicationInitializer`를 implements 하지 404 에러가 발생했다.

```java
public class ServletInitializer extends SpringBootServletInitializer implements WebApplicationInitializer {

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(DemoApplication.class);
    }

}
```

### pom.xml

배포를 담당할 파일이다. `finalName` 태그는 war 파일의 이름이다. 설정하지 않으면 demo-SNAPSHOT-0.0.1.war 파일로 만들어진다. 파일 이름을 간단히 하기 위해서 myweb 으로 지정했다. 

`outputDirectory` 경로는 war 파일이 갈 곳을 지정해준다. 이 경로는 우리가 `Volume` 으로 지정한 경로의 `autodeploy` 경로로 지정한다.

```xml
<build>
    <finalName>myweb</finalName>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <outputDirectory>D:\domain경로\autodeploy
                </outputDirectory>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### `webapp` 폴더와 `weblogic.xml` 생성.

위 그림과 같은 구조를 만드록 `weblogic.xml`을 아래와 같이 작성하자. 

![image](https://user-images.githubusercontent.com/22446581/123527475-e5d51280-d71a-11eb-8492-59a8ac0af130.png)

`context-root` 가 우리가 접속할 url 주소의 접두어다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<weblogic-web-app xmlns="http://xmlns.oracle.com/weblogic/weblogic-web-app"
                  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                  xsi:schemaLocation="http://xmlns.oracle.com/weblogic/weblogic-web-app
                  http://xmlns.oracle.com/weblogic/weblogic-web-app/1.7/weblogic-web-app.xsd">

    <context-root>/myweb</context-root>

    <session-descriptor>
        <cookies-enabled>false</cookies-enabled>
    </session-descriptor>

    <container-descriptor>
        <prefer-application-packages>
            <package-name>org.slf4j</package-name>
            <package-name>com.fasterxml</package-name>
            <package-name>org.springframework</package-name>
            <package-name>org.apache.logging</package-name>
            <package-name>org.apache</package-name>
        </prefer-application-packages>
    </container-descriptor>
</weblogic-web-app>
```

### Hello World 예제.

'hello world' 예제를 만들어보자. 

```java
@RestController
public class TestController {
    @GetMapping("/hello")
    public String test() {
        return "hello world";
    }
}
```

## Build
자 이제 빌드를 해보자. 실제 Build 파일이 어디로 생성되는지 확인해보자. `autodeploy` 폴더에 `myweb.war`파일이 생성한 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/22446581/123527609-f3d76300-d71b-11eb-8c4e-e07d530bd5d6.png)


Weblogic은 자동 배포를 위해 다른 작업을 할 수 없도록 Lock한다.
![image](https://user-images.githubusercontent.com/22446581/123527596-e326ed00-d71b-11eb-85cd-e0eaee4f269b.png)

# 결과
```
http://localhost:7001/myweb/hello
```
![image](https://user-images.githubusercontent.com/22446581/123527664-44e75700-d71c-11eb-8889-14f0aa571249.png)

# 참조


* https://blog.seri.la/14
  * intellij deploy
  
* https://forgiveall.tistory.com/613
  * 도커로 weblogic 실행.
  
* https://blueyikim.tistory.com/175
  * 개발모드 vs 운영모드
  
* https://o7planning.org/11901/deploy-spring-boot-application-on-oracle-weblogic-server

  https://www.linkedin.com/pulse/spring-boot-weblogic-14c-najeeb-arif?trk=read_related_article-card_title

  https://www.middlewareinventory.com/blog/sample-web-application-war-file-download/
  * war 개발.

* http://danielveselka.blogspot.com/2020/10/create-weblogic-docker-domain-using.html
  * domain 볼륨.
  
* https://www.lesstif.com/java/maven-deploy-plugin-8912900.html
  * deploy:deploy-file
  
* https://hakurei.tistory.com/122
  * maven 빌드 rename

