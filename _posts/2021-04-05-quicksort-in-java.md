---
published: true
layout: single
title: "Quicksort in Java"
comments: true
categories:
  - java
tags:
  - [java, trifles]
date: 2021-04-06
last_modified_at: 2021-04-06
toc: true
toc_sticky: true
---
Quick Sort는 빠른 Sort 중 하나로 매일 까먹어서 정리하기 위해 작성했다.

## 원리

![Quicksort](https://user-images.githubusercontent.com/22446581/113949586-946b6480-984a-11eb-9d08-c937ec06daf1.png){: .align-center}

 원리는 Pivot(=기준값)을 중심으로 작은 값과 큰 값을 바꾸는 방법로 정렬한다. 1차 정렬이 끝나면 정렬의 대상을 다시 분리한다. 

1. Pivot 을 결정한다.
2. 정렬한다.
3. 2개로 쪼갠다.
4. 다시 1번으로 돌아간다.

## 코드

 정렬 갯수가 적어지면, Quick Sort 보다 일반 정렬이 더 빠르다. 그래서 Quicksort를 진행하는 Limitation을 줬다.

```java
package org.example.algorithm.sort.quicksort;

import java.util.Arrays;
import java.util.Random;
import java.util.Stack;

public class Quicksort {

  private static final int QUICKSORT_SORT_LIMIT = 10;

  static void swap(int[] a, int idx1, int idx2) {
    int t = a[idx1];
    a[idx1] = a[idx2];
    a[idx2] = t;
  }

  // 퀸 정렬
  static void quickSort(int[] a) {
    Stack<Integer> leftIndexStack = new Stack<>();
    Stack<Integer> rightIndexStack = new Stack<>();

    leftIndexStack.push(0);
    rightIndexStack.push(a.length - 1);

    int startIndex,
        endIndex,
        leftPoint,
        rightPoint,
        pivot;

    while (!leftIndexStack.isEmpty()) {
      leftPoint = startIndex = leftIndexStack.pop(); // 왼쪽 커서
      rightPoint = endIndex = rightIndexStack.pop();  // 오른쪽 커서
      pivot = a[(startIndex + endIndex) / 2]; // 피벗

      if (rightPoint - leftPoint < QUICKSORT_SORT_LIMIT) {
        for (int i = leftPoint; i != rightPoint; i++) {
          for (int j = i; j != rightPoint + 1; j++) {
            if (a[i] > a[j]) {
              swap(a, i, j);
            }
          }
        }
      } else {
        do {
          while (a[leftPoint] < pivot) leftPoint++;
          while (pivot < a[rightPoint]) rightPoint--;

          if (leftPoint <= rightPoint) {
            swap(a, leftPoint++, rightPoint--);
          }
        } while (leftPoint <= rightPoint);

        if (startIndex < rightPoint) {
          leftIndexStack.push(startIndex);
          rightIndexStack.push(rightPoint);
        }
        if (leftPoint < endIndex) {
          leftIndexStack.push(leftPoint);
          rightIndexStack.push(endIndex);
        }
      }
    }
  }

  public static void main(String[] args) {

    int[] ints = new Random().ints(100, 1, 100).toArray();

    quickSort(ints);

    System.out.println(Arrays.toString(ints));
  }
}
```

## 참조

- Do it! 자료구조와 함께 배우는 알고리즘 입문 자바.