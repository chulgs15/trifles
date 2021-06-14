---
published: true
layout: single
title: "[Java] AWS Beanstalk OOM 에러."
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

 AWS에서 프로그램 고생했던 일을 기록...지금 생각해도...ㅂㄷㅂㄷ....

![그림1](https://user-images.githubusercontent.com/22446581/121832376-de6a3e00-cd04-11eb-806b-ee614a4c2487.png)

## 현상

최근 Dcoker 강의를 듣고, 회사에 맞는 기술 Stack으로 바꿔서 예제를 만들려고 했다. 

![image](https://user-images.githubusercontent.com/22446581/121832226-8fbca400-cd04-11eb-803f-f8539ef06bda.png)

사용자가 확인할 화면은 angular(frontend), 비즈니스 로직은 Spring boot(backend), proxy는 nginx 로 구성했다. frontend의 nginx는 angular의 build 결과를 보여줄 Web Server 역할을 맡는다. docker compose로 진행했을 때 정상 실행된다. 하지만 frontend와 nginx는 정상적으로 작동했지만...**backend가 죽었다. **

## 원인

### VPC 

 한달넘게 문제점을 찾았다. 일단 의심한 부분은 Spring Boot가 RDS를 찾지 못하는 오류다. 같은 VPC에 묶이지 못해 Spring boot가 RDS에 연결하면서 에러가 발생하는 현상을 찾으려고 했다. 

![image](https://user-images.githubusercontent.com/22446581/121833261-eaef9600-cd06-11eb-9f65-3398a9a43e74.png)

 Spring Boot가 RDS에 접속이 불가능하면 아래 Exception이 발생했고, 이 Exception이 발생한 Log를 찾아보았다. 하지만...발견하지 못했다.
```
com.mysql.cj.jdbc.exceptions.CommunicationsException: Communications link failure
```

VPC 연결에 문제가 있을지도 몰라 보안그룹과 VPC를 다시 생성해서 연결했지만 여전히 backend는 실행하지 못했다.

### Docker 실행 오류

Docker에 Spring boot를 실행할 때 문제가 없을까? backend를 키워드 검색해서 모든 Full Log를 검색해본 결과 아래와 같은 문구를 발견했다.

```
# /var/log/ecs/ecs-agent.log

level=warn time=2021-06-12T20:25:14Z msg="Error stopping the container; ignoring state change" ErrorName="OutOfMemoryError" error="Container killed due to memory usage" taskARN="arn:aws:ecs:us-east-2:267929877047:task/awseb-Dockerangularspringbootapp-env-xypjzeerdc/d6f0abba659d421285b2b8c713d640e8" container="backend" runtimeID="c21931adc6d4c11c19b7a2dba6b6dd96afcee954a79e7fcab89a725814b0c575"
```

`ErrorName`을 보면 `OutOfMemoryError`가 발생해서 Container가 죽고 있었다. 왜 한달 넘게 이걸 못찾았을까...라는 생각과 함께 기존 backend에 부여한 memory를 확인했다.

```json
...
    {
      "name": "backend",
      "image": "chulgs15/docker-springboot-backend",
      "hostname": "backend",
      "essential": true,
      "memory": 128
    },
...
```

할당한 memory가 너무 적어 OOM이 발생하고 있다는 생각이 들어 512로 변경했다. 정말 잘돌아갔다...ㅠㅠ.

