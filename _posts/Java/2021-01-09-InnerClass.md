---
layout: post
title:  "내부 클래스의 종류와 특징"
excerpt: ""

categories:
  - Java
tags:
  - [Java, Inner Class, Nested Class]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-10
last_modified_at: 2021-01-10
---
## 내부 클래스를 전혀 모르고 있었다.

프로그래머스 알고리즘 문제를 풀다가 코드 테스트 목적으로 main함수에서 내부클래스의 인스턴스를 선언하려했다.

다음과 같은 에러가 나고 말았다.

```java
package codesquad.isaac;

public class OuterClass {
    public int a1;
    public static int main(String[] args) {
        InnerClass t = new InnerClass();
    }

    public class InnerClass{
        public int b1;
    }
}
```

<span style="color:red">**java: non-static variable this cannot be referenced from a static context**</span>

클래스 변수가 아닌 this 변수를 static context에서 참조할 수 없다고?

<br>

지금까지 내부클래스는 일반적인 클래스랑 똑같고, 코드 위치가 클래스 안에 존재할 뿐이라고 생각했다.

그냥 InnerClass.java 파일을 만들기 귀찮아서 OuterClass 내부에 선언한 클래스일 뿐인데 왜 이런 에러가 나지? 라고 생각했다.

<br>

찾아보니 InnerClass는 코드 위치만 다를뿐인 단순한 개념은 아니었다.

InnerClass는 OuterClass와 밀첩되어 OuterClass의 private 멤버를 포함한 모든 멤버에 접근할 수 있었고,

당연히 OuterClass의 인스턴스가 특정되어야 인스턴스 Inner Class도 사용할 수 있었다.

Static Context에서 사용할 수 없었던 것이 당연했던 것이다. 

<br>

outer class 의 static method에서 인스턴스 inner class를 선언한다면,

그 inner class의 인스턴스는 자신이 어떤 outer class 인스턴스와 연관된지 모르기 때문이었다.

<br>

## 내부 클래스의 종류와 특징

| 클래스 선언 위치   | 내부 클래스                           | 특 징                                                        |
| ------------------ | ------------------------------------- | ------------------------------------------------------------ |
| 인스턴스 멤버 변수 | 인스턴스 클래스<br />(Instance Class) | 외부 클래스의 인스턴스 멤버처럼 다루어진다, 주로 외부 클래스의 <br />인스턴스멤버들과 관련된 작업에 사용될 목적으로 선언된다. |
| 스태틱 멤버 변수   | 스태틱 클래스<br />(Static Class)     | 외부 클래스의 static멤버처럼 다루어진다. 주로 외부 클래스의 static멤버<br />들과 관련된 작업에 사용될 목적으로 선언된다. |
| 지역 변수          | 지역 클래스<br />(Local Class)        | 외부 클래스의 생성자 또는 메소드 블럭에서 사용하며, 선언된 영역 내부에서만<br />사용할 수있다. |
| 지역 변수          | 익명 클래스<br />(Anonymouse Class)   | 클래스의 선언과 객체의 생성을 동시에 하는 이름없는 클래스(일회용) |

(출처: Java의 정석)

<br>

**내부 Class의 선언 위치에 따라 해당 Class를 사용할 수 있는 Context가 정해진다고 생각하면 될 것 같다.**

- 인스턴스 클래스
  - 접근 가능한 외부 클래스 멤버: 자신이 속한 OuterClass 인스턴스의 인스턴스 멤버, 스태틱 멤버
  - Context: non-static Context

- 스태틱 클래스

  - 접근 가능한 외부 클래스 멤버: OuterClass의 스태틱 멤버
  - Context: static Context

- 지역 클래스 & 익명 클래스

  - 접근 가능한 외부 클래스 멤버: 자신을 선언된 생성자 또는 메소드에서 접근가능한 OuterClass 멤버 

    ​				(Static Method라면 OuterClass의 스태틱 멤버, 일반 Method라면 OuterClass의 인스턴스 멤버, 스태틱 멤버)