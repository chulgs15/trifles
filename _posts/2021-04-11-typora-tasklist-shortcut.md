---
published: true
layout: single
title: "Typora - Task List 단축키 추가"
comments: true
classes: wide
categories:
  - blog
tags:
  - [typora, trifles]
date: 2021-04-11
last_modified_at: 2022-09-18
---

Typora에서 Task List에 단축키 추가하자.

Typora는 Checkbox를 추가하려면 'Paragraph => Task List'를 선택해야 한다. Notion 처럼 '[]'만 적어도 Checkbox가 나타나는 기능을 원해서 단축키를 추가하는 방법을 찾았다.

Typora는 단축키를 바꾸려면 `conf.user.json`파일을 수정해야 한다. 파일형식이 Json이다 보니 단축키 내용은 key는 작업, Value는 단축키로 구성되어 있다.

그리고 단축키를 바꾸기 전 Typora를 **영문**으로 바꿔주는 편이 좋다. key(작업)를 영문으로 입력해야 하기 때문이다.

# 설정방법

## 설정에 들어가기

![image](https://user-images.githubusercontent.com/22446581/114286672-dc34fa80-9a9b-11eb-9092-44effec45a69.png){: .align-center}

## 고급 설정에 들어가기

![image](https://user-images.githubusercontent.com/22446581/114286692-04245e00-9a9c-11eb-86f5-28f3b4e03dfa.png){: .align-center}

## conf.user.json 편집

꼭 conf.user.json 이다. conf.default.json을 편집하면 단축키를 추가할 수 없다.

![image](https://user-images.githubusercontent.com/22446581/114286717-3c2ba100-9a9c-11eb-9455-9db3825863fd.png){. :align-center}

원하는 단축키를 아래와 같이 편집한다.

```json
  "keyBinding": {
    // for example:
    // "Always on Top": "Ctrl+Shift+P"
    "Task List": "Ctrl+Shift+P",
    "Toggle Task Status": "Ctrl+Space"
  },
```
