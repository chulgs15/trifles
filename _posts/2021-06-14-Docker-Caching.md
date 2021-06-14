---
published: true
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

 Docker 강의를 들으면서 이미지 Cache 기능에 대한 정리.

# 결론

* Docker는 Dockerfile 내 명령어 별로 Cache를 가진다.
* Library 설치 작업은 Dockerfile 상단에 지정하자.

## 궁금증

 강의를 진행하면서 궁금한 점이 생겼다. 강의는 frontend는 `react`, backend는 `node`를 사용한다. backend의 node는 기존 아래와 같은 `Dockerfile`을 가진다.

```dockerfile
FROM node:10

COPY ./ ./

RUN npm install
```

하지만 아래와 같이 Dockerfile을 바꾸면 도커가 `npm install` 재빌드 문제가 해결할 수 있다고 한다.

```dockerfile
FROM node:10

COPY package.json ./

RUN npm install

COPY ./ ./
```

그런데 아직 부족한 내 눈에는 `COPY ./ ./`와  `COPY package.json ./` 결과 차이가 없었다. 왜 재빌드 문제를 해결해주는 것일까?

## 실험

나의 가설은 이렇다.

1. 소스에 변화가 없다면 build를 2번하지 않는다.
2. 강의에서 말하는 재빌드는 `npm`의 재빌드가 아니라, `Docker` 이미지 재빌드다.

### 기존 Dockerfile 실험

기존 `Dockerfile`로 아래와 같이 Test 했다. 두 작업의 Cache 여부를 비교해본다.

1. 소스 변경 없이 재빌드
2. 소스 수정 후 다시 빌드

 첫 번째, 소스 변경 없이 재빌드다. Cache 한 이미지를 그대로 가져온다.

![그림 1](https://user-images.githubusercontent.com/22446581/121863420-cca18e80-cd36-11eb-8a20-3a99f6150ec6.png)


두번째, 소스 수정 후 다시 빌드다.  종속성 추가 작업이 없는데,  `npm install` 작업은 Cache를 사용하지 않고 다시 실행한다.

![그림 1](https://user-images.githubusercontent.com/22446581/121863484-dd520480-cd36-11eb-8e51-14f9b6c6135b.png)

### 변경 Dockerfile 실험

변경 `Dockerfile`로 아래와 같이 Test 했다. 두 작업의 Cache 여부를 비교해본다.

1. 소스 변경 없이 재빌드
2. 소스 수정 후 다시 빌드

 첫 번째, 소스 변경 없이 재빌드다. Cache 이미지를 그대로 가져온다.

![그림 1](https://user-images.githubusercontent.com/22446581/121863807-2efa8f00-cd37-11eb-85f0-746100201735.png)

두 번째, 소스 수정 후 다시 빌드다. Cache 이미지를 그대로 사용한다.

![그림 1](https://user-images.githubusercontent.com/22446581/121864098-7da82900-cd37-11eb-9bd0-b3db9bceb8c1.png)

### 정리

 차이점은 `RUN npm install`의 cache 여부에 있다.  

|                 | 소스 수정 없이 Build | 소스 수정 후 Build |
| --------------- | -------------------- | :----------------- |
| 기존 Dockerfile | O                    | X                  |
| 변경 Dockerfile | O                    | O                  |

 정리하면, **명령어의 순서는 Docker 빌드에 영향을 준다.** 기존 `Dockerfile`은 소스에 변경이 발생하면, `COPY ./ ./` 가 아래에 있는 명령어 Cache들은 버린다. 그래서 `RUN npm install` 은 종속성을 다시 다운받아 Build를 진행한다. 

하지만 변경 `Dockerfile`은 소스에 변경이 발생해도, `COPY package.json ./`과 `npm install`가  `COPY ./ ./` 위에 있기 때문에 Cache를 사용한다.


# 참고

* https://jonnung.dev/docker/2020/04/08/optimizing-docker-images/
