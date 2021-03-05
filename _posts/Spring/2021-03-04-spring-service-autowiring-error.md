---
layout: post
title:  "Spring Service Autowiring 관련 문제"
excerpt: ""

categories:
  - Spring
tags:
  - [Spring]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-04
last_modified_at: 2021-03-04
---
# Spring - Autowiring 관련 오류

## Bean을 찾지 못해서 발생하는 오류

아래와 같이 Bean이 없다면서 Autowiring이 안 되는 경우가 있다.

```
***************************
APPLICATION FAILED TO START
***************************

Description:

Field xxxRepository in yyyService required a bean of type '어쩌구.XXXRepository' that could not be found.


Action:

Consider defining a bean of type '어쩌구.XXXRepository' in your configuration.
```

보통 스프링에서 클래스를 만들고 의존성 주입은 `@Autowired`를 통해 클래스생성을 처리하고 있다.

그런데 autowired를 하기 위해서는 `applicationContext.xml`에 해당 클래스를 bean으로 등록하는데 이것이 꽤 번거롭다. 예를 들어 아래와 같이 `autowired` 처리할 클래스를 등록해주어야 한다. 매번 클래스가 추가될 때마다 해야한다.

```
<bean id="yboardDAO" class="com.yk.yboard.dao.YboardDAOImpl" />
<bean id="yboardService" class="com.yk.yboard.service.YboardServiceImpl" />        
<bean id="yboardController" class="com.yk.yboard.control.YboardController" />
```

이럴때 자동 스프링에서 제공하는 `copoment-scan`를 이용하자. `copoment-scan`를 사용하기 위해서는 두가지 작업만 해주면 된다.

### 1. xml에서 명시

```
<context:component-scan base-package="com.yk.yboard, com.yk.common" />
```

`context:commponet-scan base-package`에서 `autowired`할 패키지경로를 기재하면 된다. 여러개의 패키지로 구분할수도 있고(콤모로 구분), 상위 패키지를 한번에 등록할 수도 있다.

### 2. autowired할 클래스에 @component로 표시

- DAO Class

```
@Component
public class YboardDAOImpl implements YboardDAO {
    ....
}
```

- Service Class

```
@Service
public class YboardServiceImpl implements YboardService {
    ....
}
```

- Controller Class

```
@Controller
public class YboardController extends YboardLogger {
    @Autowired
    private YboardService yboardService;
 	....
}
```

DAO, SERVICE의 인터페이스를 구현하는 클래스에 `@Component`를 기재해주면 된다. 다만, Controller, Service 클래스에는 이미 `@Controller` 라는 `Annotation`이 있으므로 등록하지 않아도 된다. `@Controller`, `@Service`가 이미 `@Component`를 상속하기 있기 때문이다. 이렇게 간단한 작업으로 번거로운 클래스의 xml등록은 생략될 수 있다.

출처 - [https://yookeun.github.io/java/2014/07/04/spring-component-scan/](https://yookeun.github.io/java/2014/07/04/spring-component-scan/)

