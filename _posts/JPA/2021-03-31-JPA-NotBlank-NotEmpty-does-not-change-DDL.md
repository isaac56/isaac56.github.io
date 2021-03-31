---
layout: post
title:  "JPA - @NotBlank와 @NotEmpty는 DDL에서 NOT NULL 제약조건을 생성하지 않는다."
excerpt: ""

categories:
  - JPA
tags:
  - [Spring-boot,JPA]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-31
last_modified_at: 2021-03-31
---

## @NotEmpty와 @NotBlank가 붙어도 NOT NULL 제약조건이 생성되지 않는다.

최근 validation을 테스트해보다가 알게 되었다.

@NotNull은 하이버네이트가 자동으로 해석하여 table 생성 시 NOT NULL 제약조건을 생성한다.

그리고, @NotEmpyt와 @NotBlank는 @NotNull보다 강력한 validation annotation이다.

@NotEmpty는 null이 아니면서 ""인 문자열도 아니어야 유효하고,

@NotBlank는 null이 아니면서 ' '(공백)로만 이루어진 문자열이 아니어야 유효하다.

<br>

그렇다면 @NotNull을 포함하는 강력한 조건인 @NotBlank와 @NotEmpty도, DDL생성시 NOT NULL 제약조건이 생겨야 하지 않을까?

<br>

## Bean validation 2.0부터 생긴 변경사항 이후로 제약조건이 안생기게 되었다.

2.0 이전에는 Bean Validation 에서 @NotEmpty와 @NotBlank를 제공하지 않았다.

이때까지, 많은 사람들이 Hibernate Validator의 @NotEmpty와 @NotBlank를 사용하였는데,

이 @NotEmpty와 @NotBlank는 @NotNull을 상속하였기 때문에 NOT NULL 제약조건이 생겼다고 한다.

<br>

2.0이후 버전부터는 Bean Validation에서 @NotEmpty와 @NotBlank를 제공하며,  Hibernate Validator의 @NotEmpty, @NotBlank를 사용하지 못하게 막았다고 한다.

그리고 Bean Validation에서는 @NotNull을 상속하지 않기때문에 NOT NULL 제약조건이 생기지 않는 것이다.

<br>

나같은 생각을 가진 많은 개발자들은 @NotBlank나 @NotEmpty에도 당연히 NOT NULL 제약조건이 붙을것이라고 생각할 것이다.

의도치 않게 NOT NULL 제약조건이 없다면 예상치 못한 에러가 생길 수도 있다.

<br>

## 다른 분이 Bean Validation 팀에 Issue를 전달하였고 응답을 기다리는 중이라고 한다.

역시나 나와 같은 생각을 하는 분이 계셨고, 이미 Hibernate 팀에 이슈를 전달하였다고 한다.

내가 두달만 빨랐어도 처음으로 오픈소스 기여 경험을 했을지도 모를 일이다ㅎ

같은 생각을 하신 분: <https://kafcamus.tistory.com/16>