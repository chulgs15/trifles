---
published: false
layout: single
title: "[Docker] Docker Image Cache"
comments: true
categories:
  - java
tags:
  - [trifles, docker]
date: 2021-06-14
last_modified_at: 2021-06-14
toc: true
toc_sticky: false
---


* https://blog.seri.la/14
  * intellij deploy
* https://forgiveall.tistory.com/613
  * 도커로 weblogic 실행.
* https://o7planning.org/11901/deploy-spring-boot-application-on-oracle-weblogic-server
  * war 개발.
* https://blueyikim.tistory.com/175
  * 개발모드 vs 운영모드
* https://www.linkedin.com/pulse/spring-boot-weblogic-14c-najeeb-arif?trk=read_related_article-card_title
* https://www.middlewareinventory.com/blog/sample-web-application-war-file-download/
  * war 개발.

* http://danielveselka.blogspot.com/2020/10/create-weblogic-docker-domain-using.html
  * domain 볼륨.
* https://www.lesstif.com/java/maven-deploy-plugin-8912900.html
  * deploy:deploy-file

```
docker run --name wlsnode01 -d -p 7001:7001 -p 9002:9002   -e DOMAIN_NAME=base_domain  -v C:/Users/comscg/Desktop/docker/weblogic/base_domain:/u01/oracle/user_projects/domains/:z  -v C:/Users/comscg/Desktop/docker/weblogic/properties:/u01/oracle/properties:z  store/oracle/weblogic:12.2.1.3-dev-190111

```

* https://hakurei.tistory.com/122
  * maven 빌드 rename

