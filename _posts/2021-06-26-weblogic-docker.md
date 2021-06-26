---
published: true
layout: single
title: "[Docker] Weblogic 12c 실행."
comments: true
categories:
  - java
tags:
  - [trifles, docker]
date: 2021-06-26
last_modified_at: 2021-06-26
toc: true
toc_sticky: false
---

 Weblogic을 Docker로 실행해서 Console 접근까지 이야기한다.

![로고](https://user-images.githubusercontent.com/22446581/123495508-f4f68a80-d65e-11eb-9d3b-2f2e6714abb2.jpg)

# 결론

* Docker를 사용해서 Weblogic을 실행하면 아주 편하다.

 현재 내가 있는 Site에서 큰 프로젝트가 연달아 진행 중이다. WAS의 경우 대부분 Tomcat을 사용하는데 Weblogic을 사용하는 시스템이 있다. Oracle이 가진 WAS라는 것만 알았지 실제 모습을 본적이 없다. 문제는 간간이 Weblogic 관리와 책임 문제가 내 귀에 들려왔다. ...좀 궁금해지기 시작했다.

> 도데체 Weblogic이 뭐길래...

오지랖은 죽음에 이르는 병이라더니...한번 설치부터 Spring boot 배포를 도전해보았다.

# 설치

설치는 3가지를 진행한다.

1. Docker Hub 에서 Checkout.
2. 폴더 구조 생성
3. 도커 명령어 실행.

## Docker Hub 에서 Checkout.

 다른 Docker 이미지와 달리, Weblogic은 아래 그림의 'Proceed of Checkout'에서 약관에 동의해야 이미지를 Pull 할 수 있다. 

![image](https://user-images.githubusercontent.com/22446581/123493746-5a934880-d658-11eb-9495-de28db0797b5.png)

약관을 읽어보면 2가지 주제를 가진다. 2번 30일 평가판의 경우 아직 30일이 되지 않아 경험하지 못했다.

1. 상업적으로 사용 및 배포하지 말것.
2. 라이센스를 신청 후 30일 평가판으로만 사용할 수 있다. 

긍정적인 단어(?)로 동의하면 이제 `docker pull`이 가능하다. 

## 폴더 구조 생성

 전체적인 Folder는 Spring을 고려해서 아래왁 같은 구조로 잡았다.

![image](https://user-images.githubusercontent.com/22446581/123494370-8e6f6d80-d65a-11eb-8f34-699f579c5c41.png)

 이번 글에서는 `weblogic-docker` 을 구성한다. 하위 folder는 아래와 같이 구성한다.

1. domain : 배포를 위해 `VOLUME`으로 지정할 폴더.
2. properties : Weblogic Login을 위해 Id/Password를 설정. 이 폴더도 `VOLUME`으로 지정한다.

### domain.properties

```
username=weblogic
password=weblogic123
```

 **password는 영문과 숫자를 조합해서 만들어야 한다.** 다른 예제를 보면 운영과 개발을 구별하는 `PRODUCTION_MODE`설정이 있지만, 우리는 처음부터 개발 이미지를 사용해서 추가하지 않아도 개발을 사용한다. 

## docker 명령어

docker 명령어로 Weblogic을 실행해 보자.

```powershell
docker run --name wlsnode01 -d -p 7001:7001 -p 9002:9002 -e DOMAIN_NAME=base_domain -v %cd%/domain:/u01/oracle/user_projects/domains/:z  -v %cd%/properties:/u01/oracle/properties:z  store/oracle/weblogic:12.2.1.3-dev-190111
```

완료하면 console로 접속해보자.

```
https://localhost:9002/console/
```

![image](https://user-images.githubusercontent.com/22446581/123494913-7f89ba80-d65c-11eb-94fd-2217dcd288db.png)

# 결론

 잘 돌아가는 Weblogic의 Console을 볼수 있다. 관련해서 소스는 [github](https://github.com/chulgs15/sinabro/tree/master/spring-weblogic/weblogic-docker)에 올려두었다.

![image](https://user-images.githubusercontent.com/22446581/123495024-e5764200-d65c-11eb-94d4-274a5b436869.png)

# 참고

* https://blog.seri.la/14
  * intellij deploy
* https://forgiveall.tistory.com/613
  * 도커로 weblogic 실행.
* https://blueyikim.tistory.com/175
  * 개발모드 vs 운영모드
* http://danielveselka.blogspot.com/2020/10/create-weblogic-docker-domain-using.html
  * domain 볼륨.
