---
published: true
layout: single
title: "Brute-force in java"
comments: true
categories:
  - java
tags:
  - [java, trifles]
date: 2021-04-08
last_modified_at: 2021-04-08
toc: true
toc_sticky: true
---

브푸트 포스법에 대한 간략한 설명과 사용법을 정리했습니다.

## 브루트-포스법

 브루트-포스법은 문자열 검색의 한 종류다. 문자열 검색이란 원본 패턴과 같은 패턴을 텍스트에서 검색하는 것이다. 브루트-포스는 모든 문자열을 일직선으로 검색해서 같은 패턴을 찾는다.

![brute-force.jpg](https://user-images.githubusercontent.com/22446581/113973845-f0001700-9877-11eb-8547-5646f4cccc95.jpg){: .align-center}

### 코드

`bfMatch`는 문자열의 앞에서 패턴과 일치하는 문자열을 찾으면 종료한다. 반대로 `bfMatchLast`는 문자열의 뒤에서 일치하면 종료한다. Test Code는 [Github](https://github.com/chulgs15/sinabro/blob/master/algorithm/src/test/java/org/example/algorithm/BruteForceMatchTest.java) 에 있다.

```java
public class BruteForceMatch {

  public static int bfMatch(String text, String pattern) {
    int result = -1;

    char[] texts = text.toCharArray();
    char[] patterns = pattern.toCharArray();

    if (patterns.length - texts.length > 0) {
      return result;
    }

    int mainLoppSize = texts.length - patterns.length + 1;
    mainLoop:
    for (int i = 0; i < mainLoppSize; i++) {
      if (texts[i] == patterns[0]) {
        int patternSize = patterns.length;
        for (int j = 1; j < patternSize; j++) {
          if (texts[i + j] != patterns[j]) {
            continue mainLoop;
          }
        }

        result = i;
        break;
      }
    }

    return result;
  }

  public static int bfMatchLast(String text, String pattern) {
    int result = -1;

    char[] texts = text.toCharArray();
    char[] patterns = pattern.toCharArray();

    int textSize = texts.length;
    int patternSize = patterns.length;

    if (patternSize - textSize > 0) {
      return result;
    }

    mainLoop:
    for (int i = textSize - 1; i > patternSize; i--) {
      if (texts[i] == patterns[patternSize - 1]) {
        for (int j = patternSize - 2; j != -1; j--) {
          if (texts[i + j - (patternSize - 1)] != patterns[j]) {
            continue mainLoop;
          }
        }

        result = i - patternSize + 1;
        break;
      }
    }

    return result;
  }
}

```

## 참고

* Do it!. 자료구조와 함께 배우는 알고리즘 입문 자바 편.