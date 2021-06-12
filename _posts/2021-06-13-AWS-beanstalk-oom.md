---
published: false
layout: single
title: "[Java] AWS Beanstalk OOM 개고생."
comments: true
categories:
  - java
tags:
  - [serious, spring, docker, beanstalk, aws]
date: 2021-06-13
last_modified_at: 2021-06-13
toc: true
toc_sticky: true
---

 AWS에서 고생했던 일을 기록...지금 생각해도...ㅂㄷㅂㄷ....

* 현상

  * 내가 구현하려고 했던 프로그램은 무엇이었나?
    * 최근 Docker 강의를 듣고 우리에게 맞는 언어로 다시 구축을 해보았다.
    * 구현은 그림과 같이 frontend 는 angular, backend는 Spring boot, DB 는 mysql을 사용했다.
    * Local에서는 문제가 없이 수행하는 것을 보고 Beanstalk에 이관하려고 했다.
  * 오류 발생.
    * 하지만 구현한 nginx 와 frontend는 문제가 없었지만, Spring boot는 문제가 올라오지 못했다.

* 원인

  * 한달 가까이 찾았던 것 같다.

  * 시도

    * VPC 가 잘못되어 RD에 접속하지 못하고 Spring Boot가 죽고 있다.

    * 하지만 접속이 불가능하면 Exception 이 발생하면서 정확한 오류를 알려주었다.

      ```
      com.mysql.cj.jdbc.exceptions.CommunicationsException: Communications link failure
      ```

      

  * 하지만 다른 곳에서 오류를 찾았다.

    * 로그를 보여주자

    ```
    # /var/log/ecs/ecs-agent.log
    
    level=warn time=2021-06-12T20:25:14Z msg="Error stopping the container; ignoring state change" ErrorName="OutOfMemoryError" error="Container killed due to memory usage" taskARN="arn:aws:ecs:us-east-2:267929877047:task/awseb-Dockerangularspringbootapp-env-xypjzeerdc/d6f0abba659d421285b2b8c713d640e8" container="backend" runtimeID="c21931adc6d4c11c19b7a2dba6b6dd96afcee954a79e7fcab89a725814b0c575"
    ```

    * 아니 ㅅㅂ... oom 이라니...
    * 로그가 잘 안보여도 이렇게 안보일 수 있나?

* 조치.

  * 뭐시기 json 에서 메모리를 올렸다.
  * 기존 Json / 변경 json
  * 정상 작동한다.
