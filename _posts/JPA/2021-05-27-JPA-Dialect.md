---
layout: post
title:  "JPA - Dialect(방언) 설정하기"
excerpt: ""

categories:
  - JPA
tags:
  - [Spring-boot,JPA]

toc: true
toc_sticky: true
comments: true
 
date: 2021-05-27
last_modified_at: 2021-05-27
---

## Dialect(방언)이란?



SQL은 표준 ANCI SQL이 있고 DBMS Vendor들이 (MsSql, Oracle, MySql, PostgreSql) 제공하는 SQL이 존재한다.

표준 SQL은 모든 DBMS에서 사용할 수 있는 표준 언어이고, 

각 DBMS에서 독자적인 기능을 사용하기 위해 각자가 SQL을 만들어 추가할 수 있다.



그리고, JPA는 대부분 개발자가 SQL을 직접 작성하지 않고 Entity 객체를 참고하여 JPA가 직접 SQL을 만든다.

여기서 JPA가 어떤 DBMS에 맞춰서 SQL을 사용할지 알려주는 것이 Dialect(방언, 사투리)를 설정하는 것이다.



## Spring boot에서 Dialect 설정

application.properties에서 설정해줄 수 있다.



- Mysql5로 설정

```
spring.jpa.database-platform=org.hibernate.dialect.MySQL5Dialect
```

- OracleDB 10g로 설정

```
spring.jpa.database-platform=org.hibernate.dialect.Oracle10gDialect
```



