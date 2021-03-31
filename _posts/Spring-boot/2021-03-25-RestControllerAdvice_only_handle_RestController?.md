---
layout: post
title:  "Spring-boot: RestControllerAdvice에서 RestController의 예외만 받기"
excerpt: ""

categories:
  - Spring-boot
tags:
  - [Spring-boot, Transactional]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-25
last_modified_at: 2021-03-25
---

RestControllerAdvice에 ExceptionHandler를 정의하면 RestController에서 발생한 Execption만 처리하고,

ControllerAdvice에 ExceptionHandler를 정의하면 Controller에서 발생한 Exception만 처리하는 줄 알았다.

<br>

RestController와 RestControllerAdvice의 코드를 확인해보면

`@RestController` = `@Controller` + `@ResponseBody` ,

`@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody` 일 뿐이다.

<br>

즉, 따로 설정을 하지 않으면, Controller에서 발생한 Exception을 RestControllerAdvice에서 처리할 수 있다.

마찬가지로 RestController에서 발생한 Exception을 ControllerAdvice에서 처리할 수 있다.

<br>

처음 의도한대로 RestController의 Exception만 처리하려면

`@RestControllerAdvice(annotations = RestController.class)` 어노테이션을 붙여주면 된다.

(ControllerAdvice도 마찬가지)

<br>

참고: <https://woowacourse.github.io/javable/post/2020-07-28-global-exception-handler/>

