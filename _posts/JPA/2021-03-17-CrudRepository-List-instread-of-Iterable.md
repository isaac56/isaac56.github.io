---
layout: post
title:  "CrudRepository-Iterable<T>를 List<T>로 반환하고 싶을 때"
excerpt: ""

categories:
  - JPA
tags:
  - [Spring-boot,JPA]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-17
last_modified_at: 2021-03-17
---

CrudRepository<T, U>를 상속하여 DAO를 사용하다 의문점이 생겼다.

CrudRepository는 findAll과 같이 복수의 Entity를 반환하는 경우 ```Iterable<T>```를 반환한다. 

```List<T>``` 로 반환받아 사용하고 싶을 땐, Guava를 사용하거나 직접 List로 Converting하는 메소드를 만들어서 바꿔야 할까?

찾아보니, 간단하게 바꿀 수 있는 방법이 있었다.

DAO내부에서 findAll과 같은 메소드의 반환 타입을 List로 바꾸어 오버라이드하면 spring이 알아서 Converting해준다고 한다.

```java
@Repository
public interface DataRepository extends CrudRepository<Data, Long> {

    @Override
    List<Data> findAll();

}
```

```java
@NoRepositoryBean
public interface GenericRepository<T> extends CrudRepository<T, Long> {

    @Override
    List<T> findAll();

}
```



출처 - <https://stackoverflow.com/questions/34702252/spring-data-jpa-crudrepository-returns-iterable-is-it-ok-to-cast-this-to-list>