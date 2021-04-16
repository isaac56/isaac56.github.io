---
layout: post
title:  "bug report: jackson.databind.exc.InvalidDefinitionException"
excerpt: ""

categories:
  - Spring-boot
tags:
  - [Spring-boot]

toc: true
toc_sticky: true
comments: true
 
date: 2021-04-16
last_modified_at: 2021-04-16
---
## 팀원과 페어프로그래밍 도중에 아래와 같은 에러가 발생하였다.

```
Type definition error: 

[simple type, class team9.todo.domain.DTO.Card.RequestCreateDTO]; nested exception is com.fasterxml.jackson.databind.exc.InvalidDefinitionException: 

Cannot construct instance of `team9.todo.domain.DTO.Card.RequestCreateDTO`

(no Creators, like default constructor, exist): cannot deserialize from Object value (no delegate- or property-based Creator)\n 

at [Source: (PushbackInputStream); line: 2, column: 5]"
```

Request body에 json으로 받은 정보를, 내가 만든 RequestCreateDTO로 변환하는 과정에서 오류가 생긴 것 같았다.

## 문제는 팀원의 로컬에서 spring을 실행할 때만 아래와 같은 에러가 발생한다.

내 로컬 환경에서는 잘 돌아갔고 저 에러메세지가 뜨지 않는다.

무슨 문제인지 찾아보다가, 유일한 차이점을 발견하였다.

인텔리제이 설정(Preferecne) -> Build, Excecution, Deployment -> Build Tools -> Gradle -> Build run using 의 설정이 달랐다.

나는 Gradle로 설정을 해놨지만, 팀원은 IntelliJ IDEA로 설정을 해놨다.

팀원 로컬도 Gradle로 바꿨더니 에러가 해결되었다.

## Build run using 값을 바꾸는 부분에 보면 다음과 같은 메세지가 있다.

```
By default IntelliJ IDEA uses Gradle to build the project and run the tasks.

In a pure Java/Kotlin project, building and running by means of the IDE might be faster, thanks to optimizations. Note, that the IDE doesn't support all Gradle plugins and the project might not be built correctly with some of them.
```

해석하자면 순수한 Java/Kotlin 프로젝트는 ItelliJ IDEA로 설정을 하는게 더 빠를 수도 있다.

그러나 IntelliJ IDEA로 설정을 하면 모든 gradle plugin을 지원하지 않기 때문에 프로젝트가 정상 동작하지 않을 수 있다고 한다.

## 결론

코드, 설정, gradle 의존성 등이 모두 동일한데도 자기만 에러가 난다면, IDE의 Build run using 을 확인해보자.