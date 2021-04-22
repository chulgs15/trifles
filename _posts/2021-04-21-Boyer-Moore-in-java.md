---
published: true
layout: single
title: "Boyer-Moore in Java"
comments: true
categories:
  - java
tags:
  - [algorithm]
date: 2021-04-21
last_modified_at: 2021-04-22
toc: true
toc_sticky: true
---
문자 검색 알고리즘인 Boyer-Moore 에 대해서 정리.

# Boyer-Moore 법칙

## 목적

먼저 KMP 법칙을 소개하려고 했다. 하지만 Boyer-Moore 법칙이 훨씬 좋다는(?) 의견이 KMP 법칙에 말미에 써있어 내용을 급선회했다.



## Boyer-Moore 기본 순서

<img src="https://user-images.githubusercontent.com/22446581/115511815-3e6fd580-a2bc-11eb-9c75-9c52be670372.png" alt="image" style="zoom:33%;" />

기본적인 순서는 아래와 같다.

1. 먼저 중요한 점은 Pattern의 역순으로 Text를 비교한다.
2. 역순으로 비교했을 때, 비교하는 첫번째 문자가 다르면 이동한다.

 순서 2번에서 이동 할 길이를 얼마로 정하느냐가 중요하다. 위 그림처럼 Pattern의 길이 3만큼 이동할 수 있지만, 아닌 경우가 있을 수 있다.

<img src="https://user-images.githubusercontent.com/22446581/115513430-049fce80-a2be-11eb-9d3f-603f7abcc5f0.png" alt="image" style="zoom: 50%;" />

 Text가 'DEABCF', Pattern이 'ABC'라면 2만큼 이동하면 맞는 글자를 찾을 수 있다. 즉, 비교하려는 Text 문자가 Pattern에 존재하는 문자에 따라 이동길이가 달라진진다. 참고한 책에서 ''건너띄기 표''라는 개념이 나온다.

### 건너띄기 표 개념

 내가 참고한 책에는 '건너띄기 표'라는 개념이 있다. 모든 Character를 배열에 담아, index=key와 value=건너뛸 길이를 저장한다. 아래 그림에서 보면 건너띄기 표를 저정해서 index를 바꿀 위치까지 계산한다.

<img src="https://user-images.githubusercontent.com/22446581/115527142-c0b3c600-a2cb-11eb-9928-89623d9818d3.png" alt="image" style="zoom: 33%;" />

1. Text 비교 대상이 Pattern 집합에 존재하는 문자라면 건너띄기 표를 참조해서이동한다. 
2. Text 비교 대상이 Pattern 집합에 존재하지 않는다면 Pattern의 길이만큼 이동한다.

## 코드

### 건너띄기 표

```java
int[] skip = new int[Character.MAX_VALUE + 1];

// 건너뛰기 표 만들기
for (pt = 0; pt <= Character.MAX_VALUE; pt++) {
    skip[pt] = patternLen;
}

for (pt = 0; pt < patternLen - 1; pt++) {
    skip[pattern.charAt(pt)] = patternLen - pt - 1;
}
```

책에서 언급한 건너띄기 표를 구성하는 방법이다. Java가 지원하는 모든 Character를 배열의 Key로 입력하고 이동 길이를 Value 로 입력한다. 

 내 의견은 건너띄기 표 생성이 너무 Heavy한 작업이라 생각한다. 문자열을 찾는 작업을 시작할 때마다, 건너띄기 표를 만드는 작업은 필요하지만 반복적인 작업이다. 물론 Singleton으로 선언에서 어떤 작업을 실행하더라도 1번만 생성 후 참조하는 방법도 있다. Singleton으로 유지하는 메모리 Cost가 신경쓰여 아래와 같이 만들었다.

### Boyer-Moore

Pattern에 존재하는 문자열을 만났을 때, 1만큼만 이동한다. 반대의 경우는 문자의 길이만큼 이동한다.

```java
public class BoyerMooreMatch {

    public static int match(String text, String pattern) {
        Set<Character> set = pattern.chars().mapToObj(x -> (char) x).collect(Collectors.toSet());

        for (int i = 0; i <= text.length() - pattern.length(); ) {
            int j = pattern.length() - 1;

            while (text.charAt(j + i) == pattern.charAt(j) && j != 0) {
                j--;
            }

            if (j == 0) {
                return i;
            } else {
                if (set.contains(text.charAt(j + i))) {
                    i++;
                } else {
                    i += pattern.length();
                }
            }
        }

        return -1;
    }


    public static int matchLast(String text, String pattern) {
        Set<Character> set = pattern.chars().mapToObj(x -> (char) x).collect(Collectors.toSet());

        if (pattern.length() > text.length()) {
            return -1;
        }

        for (int i = text.length() - pattern.length(); i != 0; ) {
            int j = pattern.length() - 1;

            while (text.charAt(i + j) == pattern.charAt(j) && j != 0) {
                j--;
            }

            if (j == 0) {
                return i;
            } else {
                if (set.contains(text.charAt(i + j))) {
                    i--;
                } else {
                    i -= pattern.length();
                }
            }
        }

        return -1;
    }

}
```

## 참조

* https://stackoverflow.com/questions/36432450/how-to-fetch-unique-characters-in-string
  * 문자열 Unique 하게 만들기.