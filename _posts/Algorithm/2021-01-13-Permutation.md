---
layout: post
title:  "Java로 순열 구현"
excerpt: ""

categories:
  - Algorithm
tags:
  - [Java, Permutation]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-13
last_modified_at: 2021-01-13
---
## 자바에는 기본제공 순열 함수가 없다 ㅠㅠ

C++로 코딩테스트를 풀다가 자바로 넘어갔을 때 가장 당황했던 것이었다.

그러나 진즉에 알고 있었어야 하는 기본 알고리즘이다.

STL에 의존해서 알아보려고 하지 않았던 Permutation에 대하여 알아보고 구현해보겠다.

<br>

## ABC를 가지고 상황을 생각해보자

[A, B, C] -> 이렇게 생긴 리스트를 가지고 만들 수 있는 순열을 생각해보자.

![1](/public/img/Permutation/1.gif)

>  출처 : [http://www.eandbsoftware.org/print-all-permutations-of-a-given-string/](http://www.eandbsoftware.org/print-all-permutations-of-a-given-string/)

초기 상태가 A라고 한다면 다음과 같이 경우의 수가 나온다.

1. 첫번째에 A가 위치
   1. 두번째에 B가 위치
      - ABC
   2. 두번째에 C가 위치
      - ACB
2. 첫번째에 B가 위치
   1. 두번째에 A가 위치
      - BAC
   2. 두번째에 C가 위치
      - BCA
3. 첫번째에 C가 위치
   1. 두번째에 B가 위치
      - CBA
   2. 두번째에 A가 위치
      - CAB

<br>

위 과정을 이해하고 코드로 구현하면 아래와 같이 간단하게 구현이 가능하다

```java
public class Permutation {
    public static void permutation(int[] arr, int depth){
        if(depth == arr.length - 1)
            printArr(arr);

        for(int i = depth; i < arr.length; i++) {
            swap(arr, depth, i);
            permutation(arr, depth+1);
            swap(arr, depth, i);
        }
    }

    public static void printArr(int[] arr){
        for(int e : arr) {
            System.out.print(e + " ");
        }
        System.out.println();
    }

    public static void swap(int[] list, int i, int j) {
        int temp = list[i];
        list[i] = list[j];
        list[j] = temp;
    }

    public static void main(String[] args) {
        int[] arr = new int[]{1,2,3};
        permutation(arr, 0);
    }
}
```

<br>

## String의 Permutation을 매우 간단하게 구현

[프로그래머스 - 소수찾기](https://programmers.co.kr/learn/courses/30/lessons/42839)를 풀다가 String을 매우 간단하게 permutation한 함수를 발견하여 추가해놓았다.

위와 핵심은 똑같다, 맨 앞자리부터 들어갈 수 있는 요소들을 집어넣는 것.

그러나 자바에서 String 은 +로 쉽게 String끼리 이어붙일 수 있기 때문에 아래와 같은 구현이 가능했다.

```java
//사용법 permutation("",str);
public void permutation(String prefix, String str){
    if(str.equals("")) System.out.println(prefix);
    
    for(int i = 0; i < str.length(); i++) {
        permutation(prefix + str.charAt(i), str.substring(0, i) + str.substring(i+1, str.length()));
    }
}
```

<br>

## C++ STL과 같이 next_permutation을 구현하기

또 다른 방법으로 STL의 next_permutation을 구현할 수 있다고 한다.

일단 위의 백트래킹으로 구현한 permutation을 사용할 예정이므로 next_permutation은 참고할 만한 글의 주소를 남겨놓아야겠다.

(나중에 공부해야겠다.)

[https://velog.io/@junhok82/Java로-순열Permutation-구현하기](https://velog.io/@junhok82/Java로-순열Permutation-구현하기)

