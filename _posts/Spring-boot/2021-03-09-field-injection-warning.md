---
layout: post
title:  "Spring-boot: 생성자 주입을 사용해야 하는 이유, 필드 인젝션이 좋지 않은 이유"
excerpt: ""

categories:
  - Spring-boot
tags:
  - [Spring-boot]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-09
last_modified_at: 2021-03-09
---
스프링을 사용하여 DI(Dependency Injection)을 할 때, 아무 생각 없이 @Autowired를 사용한 필드 주입을 사용하곤 했다.

사실 첫 사용부터 intellij에서 Field injection에 대하여 경고가 뜨곤 했는데 크게 신경안쓰고 있었다.

이번 기회에 해당 경고가 왜 뜨는지 찾아보고 아래 글을 기반으로 학습하게 되었다.

[https://leejisoo860911.tistory.com/2](https://leejisoo860911.tistory.com/2)

[https://yaboong.github.io/spring/2019/08/29/why-field-injection-is-bad/](https://yaboong.github.io/spring/2019/08/29/why-field-injection-is-bad/)

