---
layout: post
title:  "JPA - @NotNull vs @Column(nullable = true)"
excerpt: ""

categories:
  - JPA
tags:
  - [Spring-boot,JPA]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-30
last_modified_at: 2021-03-30
---

## @NotNull과 @Column(nullable=true) 의 차이가 뭘까?

엔티티 필드에 선언하게 되면, 두 어노테이션 모두 동일한 결과를 보이는 듯 하다.

둘 모두 DB 테이블 생성시, 해당하는 칼럼에 not null 제약 조건이 생긴다.

그렇다면 차이점이 무엇일까?

<br>

## 1. 종속성이 다르다(위치한 라이브러리가 다르다.)

@Column 은 JPA에서 기본 제공하는 어노테이션이므로, 별다른 종속성을 추가할 필요가 없다.

그러나 @NotNull 어노테이션은 BeanValidation API에서 제공하므로, @NotNull을 추가하려면 빌드 도구에 종속성을 추가해야한다.

```java
//build.gradle의 경우
implementation group: 'javax.validation', name: 'validation-api', version: '2.0.1.Final'
```

<br>

## 2. 체크하는 시스템이 다르다.

@Column(nullable = true) 는 DB 테이블 생성시에 not null 제약조건을 추가할 뿐이다.

해당 칼럼에 null이 할당된 채로 DB에 저장하려고 하더라도, Java Application에서는 검사하지 않는다.

SQL문이 실행되면서 not null 제약 조건에 걸려 실패할 뿐이다.

<br>

@NotNull 은 테이블 생성시에 not null 제약조건을 추가할 뿐 아니라, 

해당 엔티티를 DB에 저장하려고 할 경우 Java application에서 해당 필드가 null인지 검사하게 된다.

즉 JPA의 life-cycle중 pre-persist나 pre-update 시점에서 해당 필드가 null인지 검사하게 된다.

<br>

@Column(nullable = true)은 DB에서 null인지 체크하게 되고,

@NotNull은 java application에서 null인지 체크하게 된다.

<br>

## 3. 체크하는 시점이 다르다.

위에서 설명한 바와 같다.

@Column(nullable = true)는 SQL문이 실행되는 시점에 not null 제약조건을 통해 체크하게 된다.

(해당 어노테이션은 단순히 테이블 생성시 제약조건을 만들어줄 뿐이다.)

반면, @NotNull은 pre-persist 또는 pre-update 시점에 null인지 체크하게 된다.

<br>

## 어떤 걸 사용하는게 좋을까?

내가 참고한 글의 글쓴이는 @NotNull을 사용하길 추천한다.

당연한 것이, 잘못된 SQL이 실행되기 전에 java application 단에서 체크할 수 있기 때문이다.

> Database constraints are an important tool to ensure data consistency, but you shouldn’t rely on them in your Java application.



출처 : <https://thorben-janssen.com/hibernate-tips-whats-the-difference-between-column-nullable-false-and-notnull/>

