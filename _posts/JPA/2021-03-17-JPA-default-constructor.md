---
layout: post
title:  "JPA Entity의 기본 생성자가 필요한 이유"
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

jpa를 이용하여 엔티티를 작성하는 경우, 파라미터로 초기화하는 생성자를 정의하는 경우가 많다.

이 경우 빈 생성자를 명시하지 않으면 ```No default constructor for entity:``` 와 같은 에러가 발생하게 되는데,

하이버네이트 등의 프레임워크에서 리플렉션을 이용해 해당 객체를 생성할 때 해당 객체(엔티티)의 기본 생성자를 호출하게 되는데 위와 같이 파라미터가 필요한 생성자로 재정의 한다면

```
Class<T>.newInstance
```

를 이용해서 해당 엔티티의 객체를 만들어 낼 수 없게 되기 때문이다.

<br>

“엔티티는 반드시 파라미터가 없는 (no-arg) public 또는 protected 생성자가 있어야 한다.” - JPA 2.0 스펙