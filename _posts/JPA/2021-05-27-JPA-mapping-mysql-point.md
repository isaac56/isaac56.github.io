---
layout: post
title:  "JPA - mysql Point 타입을 JPA로 삽입할 때 에러 발생"
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

## Data truncation: Cannot get geometry object from data you send to the GEOMETRY field

mysql의 point 타입을 JPA로 다루기 위하여, org.locationtech.jts.geom.Point 타입으로 필드를 선언하였다.

그 후 Entity를 저장하려는데 

```
Data truncation: Cannot get geometry object from data you send to the GEOMETRY field
```

위와 같은 에러가 발생,

스택오버플로우를 찾아보니, spatial(공간) type을 다루기 위한 jpa의 [dialect](https://isaac56.github.io/jpa/2021/05/27/JPA-Dialect.html)설정이 필요한 것같다.

application.proerties에 아래와 같이 추가하여 해결하였다.

```
spring.jpa.database-platform=org.hibernate.spatial.dialect.mysql.MySQL5InnoDBSpatialDialect
```



stackoverflow - <https://dba.stackexchange.com/questions/170787/cannot-get-geometry-object-from-data-you-send-to-the-geometry-field>

