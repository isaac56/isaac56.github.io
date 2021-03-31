---
layout: post
title:  "DTO vs VO vs Entity"
excerpt: ""

categories:
  - Spring-boot
tags:
  - [Spring-boot]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-17
last_modified_at: 2021-03-17
---

1. DTO

   - Layer 간에 데이터를 전송하는 객체이다.

   - getter와 setter만으로 이루어져 있다.
   - 접근 메소드 외의 메소드(로직)을 갖지 않는다.
   - 생성 후에도 값이 바뀔 수 있다(mutable)

2. [VO](https://isaac56.github.io/java/2021/02/19/Value-Object.html)

   - 값을 나타내는 객체이다.
   - 내부의 값이 같다면 그 두 인스턴스는 같은 객체이다.(hashCode와 equals를 Override해야한다.)
   - 접근 메소드 외의 메소드(로직)를 가질 수 있다.
   - 생성 후엔 GC에 의해 소멸될 때까지 값이 바뀌지 않는다(imutable)

3. Entity

   - DB Table에 맵핑되는 객체이다.(인스턴스는 row에 해당)
   - 접근 메소드 외의 메소드(로직)를 가질 수 있다.
   - 가변 객체이다(mutable)

