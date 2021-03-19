---
layout: post
title:  "@Transactional"
excerpt: ""

categories:
  - TIL
tags:
  - [Spring, Transactional]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-19
last_modified_at: 2021-03-19
---

1. 스프링의 트랜잭션 지원
   - 스프링은 코드 기반의 트랜잭션 처리(Programmatic Transaction) 뿐만 아니라 선언적 트랜잭션(Declarative Transaction)을 지원하고 있다. 스프링이 제공하는 트랜잭션 템플릿 클래스를 이용하거나 설정 파일, 어노테이션을 이용해서 트랜잭션의 범위 및 규칙을 정의할 수 있다.

2. 선언적 트랜잭션 처리
   - 선언적 트랜잭션은 설정 파일이나 어노테이션을 이용해서 트랜잭션의 범위, 롤백 규칙 등을 정의

## @Transactional

- 보통 Service나 Service 내부의 메소드에 선언하는 어노테이션
- @Transactional 메소드 내부에서 Entity를 수정하게 되면 dirty checking을 통해 자동으로 DB에 반영된다.
- @Transactional 메소드가 정상적으로 종료되지않으면 DB에 반영되지 않는다. (Atomicity 보장)
- @Transactional(readonly=true) 로 설정할 경우 Entity를 수정해도 DB에 저장하지 않는다.(readonly의 default는 false 값이다.)
- 서로 다른 두 쓰레드가 @Transactional 메소드를 통해 동일 Entity에 접근한다면 lock을 걸어서 동시에 접근할 수 없게 한다. (Isolation 보장)
- 서로 다른 두 쓰레드가 @Transaction(readonly=true) 메소드를 통해 동일 Entity에 접근한다면 lock을 걸 필요는 없다. (읽기만 하기 때문에)

<br>

스프링에서는 @Transactional 어노테이션을 통해 메소드에서 접근한 Entity의 원자성(Atomicity), 독립성(Isolation)을 보장해준다.

**(학습중인 내용이기 때문에 내용이 부정확할 수 있습니다. 추후에 업데이트할 예정입니다.)**

[ACID](https://ko.wikipedia.org/wiki/ACID)

[Transactional 과 Synchrnoized](https://stackoverflow.com/questions/41767860/spring-transactional-with-synchronized-keyword-doesnt-work)

