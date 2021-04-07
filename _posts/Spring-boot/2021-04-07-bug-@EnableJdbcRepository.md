---
layout: post
title:  "bug report: @EnableJdbcRepositories의 위치로 인해 Repository를 찾지 못하는 문제"
excerpt: ""

categories:
  - Spring-boot
tags:
  - [Spring-boot, JDBC]

toc: true
toc_sticky: true
comments: true
 
date: 2021-04-07
last_modified_at: 2021-04-07
---

### @EnableJdbcRepository 의 위치로 인해 Repository Bean을 찾지 못했다.

분명 UserRepository를 생성하고, @Repository 선언으로 Bean 등록을 해줬음에도 아래와 같은 에러가 계속 발생했다.

```
***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of constructor in team9.todo.controller.ApiUserController required a bean of type 'team9.todo.repository.UserRepository' that could not be found.


Action:

Consider defining a bean of type 'team9.todo.repository.UserRepository' in your configuration.
```

<br>

### @EnableJdbcRepository를 선언한 @Configuration 클래스 위치가 상위 패키지에 있지 않았기 때문이었다.

> Annotation to enable JDBC repositories. Will scan the package of the annotated configuration class for Spring Data repositories by default

@EnableJdbcRepository의 설명을 보면 어노테이션이 붙은 클래스가 위치한 패키지부터 repository 객체들을 scan 한다고 적혀있다.

내 프로젝트의 패키지 구조는 다음과 같았다.

```
team9.todo
	├ config
	├ controller
	├ repository
```

여기서 @EnableJdbcRepository가 선언된 Configuration 클래스는 team9.todo.config 패키지에 위치해 있었다.

따라서 team9.todo.config 부터 repository를 스캔하여 Bean으로 등록하기 때문에, 

백날 team9.todo.repository에 클래스를 만들어봤자 Bean 등록이 안됐던 것이다.

@EnableJdbcRepository("team9.todo.repository")을 통해 team9.todo.repository를 스캔하도록 직접 설정해줘서 해결하였다.

<br>

### @Enable000Repository ("000은 Jdbc, Jpa 등이 들어올 수 있음") 은 뭘까?

위와 같은 문제로 많은 삽질이 있었는데, 그 이유는 @Enable000Repository 가 무슨 역할을 하는지 왜 필요한지 등을 잘 몰랐기 때문이다.

그 역할은 다음과 같다.

예를 들어 @EnableJdbcRepository를 선언해주면, 선언된 클래스가 위치한 패키지부터 스캔을 시작한다.

그리고 아래처럼 Repository를 상속한 interface를 읽었을 때, Jdbc를 사용하기 알맞게 구현체를 만들어 Bean 객체로 등록해준다.

```java
public interface UserRepository extends CrudRepository<User, Long> {
}
```

<br>

### @EnableJpaRepository 를 선언하지 않아도 잘 돌아가던데?

그 이유는 spring boot의 auto configuration 기능 때문이다.

기본적으로 spring boot의 메인 클래스에는 @SpringBootApplication이 붙어있다.

@SpringBootApplication은 다시 @EnableAutoConfiguration이 붙어있는 걸 확인할 수 있는데,

@EnableAutoConfiguration이 있으면 알아서 프로젝트의 dependency에 따라 @EnableJpaRepository 또는 @EnableJdbcRepository를 활성화 시키기 때문이었다.

<https://springbootdev.com/2017/11/13/what-are-the-uses-of-entityscan-and-enablejparepositories-annotations/>






