---
layout: post
title:  "@Autowired 없어도 DI 해주는 경우?"
excerpt: ""

categories:
  - Spring
tags:
  - [Spring, Transactional]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-29
last_modified_at: 2021-03-29
---

spring 4.3 이후부터는, 생성자가 하나일 경우에는 [@Autowired](https://github.com/Autowired) 없이도 자동으로 해당 생성자를 통해 constructor injection을 해준다고 한다.

<https://docs.spring.io/spring-framework/docs/4.3.x/spring-framework-reference/htmlsingle/#beans-autowired-annotation>

<br>

따라서 생성자가 두개 이상일 경우에는,

constructor injection을 위한 생성자 하나에 @Autowired 어노테이션을 붙여줘야 한다.

<br>

개인적으로 그냥 헷갈리기 싫으면 항상 @Autowired를 붙이는게 나을 것 같다.

