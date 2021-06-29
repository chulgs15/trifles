---
published: false
layout: single
title: "[Docker] Angular를 Weblogic 12c에 이관"
comments: true
categories:
  - java
tags:
  - [trifles, docker, angular, weblogic]
date: 2021-06-28
last_modified_at: 2021-06-28
toc: true
toc_sticky: false
---

 Weblogic으로 Angular를 실행해보자.

![프레젠테이션1](https://user-images.githubusercontent.com/22446581/123760567-ef69a080-d8fb-11eb-8f47-8fcb8879c160.png)

# 결론

* Weblogic 에서 정적 html을 서비스 할 수 있다.

이왕 여기까지 왔으니...Angular까지 서비스를 만들려고 한다. Spring boot와 마찬가지로 Angular를 Weblogic에서 자동으로 배포할 수 있는 방법을 이야기한다.

# 과정

![흐름도](https://user-images.githubusercontent.com/22446581/123760231-9dc11600-d8fb-11eb-8cc4-04a71b6467f8.png)

1. Angular 프로젝트 생성.
2. angular.json 설정 수정.
3. Build

## Angular 프로젝트 생성.

1. Intellij 에서 Angular 프로젝트 생성

![image](https://user-images.githubusercontent.com/22446581/123763115-70299c00-d8fe-11eb-8a2b-df4ec223bd9a.png)

2. module은 `frontend`로 생성한다.

![image](https://user-images.githubusercontent.com/22446581/123763289-951e0f00-d8fe-11eb-99b2-ce16843ff3ad.png)

3. 아래와 같이 project 구조를 완성한다.

![image](https://user-images.githubusercontent.com/22446581/123763565-e4fcd600-d8fe-11eb-82bb-c82963a1e89b.png)

이전 Spring Boot를 유지하고 frontend를 추가로 만든 구조를 확인한다.

## package.json 과 angular.json 수정

Weblogic 자동 배포를 위해, package.json과 angular.json 을 수정해야 한다.

1. package.json의 ng build를 수정.
2. angular.json의 outputPath과 assets를 수정

### package.json의 ng build를 수정.

package.json은 시작/배포 명령어가 들어있다. 이중 build 명령어를 아래와 같이 수정한다.

```
"build": "ng build --base-href /frontend/"
```

base-href를 추가하지 않으면 css, js 파일들이 /frontend 이 빠진 상태로 찾아 오류가 발생한다. base-href 를 추가하면 아래와 같은 경로가 나타난다. 

```html
<!DOCTYPE html>
<html lang="en">
<head>
...
    <base href="/frontend/">
...
</head>
```

### angular.json의 outputPath와 assets를 수정.

#### assets 수정.

weblogic에 배포할 때는 WEB-INF에 web.xml과 weblogic.xml을 추가해야 한다. 

하지만 build 할 때는 이 파일들을 포함하지 않는다.

build할 때 포함할 수 있도록 프로그램을 





## Build









# 결과

아래 URL로 접속해서 결과를 확인해보자.

```
http://localhost:7001/frontend/
```

![image](https://user-images.githubusercontent.com/22446581/123760746-1d4ee500-d8fc-11eb-852a-99fed812833a.png)

# 참조

* http://sauvikb.blogspot.com/2019/02/deploying-angular-app-to-weblogic-12c.html
  * angular 연동
* https://stackoverflow.com/questions/51182322/whats-the-difference-between-base-href-and-deploy-url-parameters-of-angular
  * base-url / deploy-url
* https://stackoverflow.com/questions/19786142/what-is-web-inf-used-for-in-a-java-ee-web-application
  * WEB-INF

