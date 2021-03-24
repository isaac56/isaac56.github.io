---
layout: post
title:  "CrudRepository의 save 메서드가 getter를 호출하여 NPE 발생?"
excerpt: ""

categories:
  - JPA
tags:
  - [Spring-boot,JPA]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-25
last_modified_at: 2021-03-25
---

실수로 Entity의 id 필드를 Wrapper class로 선언하고, getter의 return type으로 primitive type으로 선언 해버렸다.

Entity 클래스이기 때문에, new로 생성할 때만 id가 null값을 가지고 DB에 저장되는 순간 auto increment로 id가 부여된다.

새로운 Entity를 생성할 때만 new로 생성하고 모든 Entity는 DB에서 꺼내쓰기 때문에, 생성할 때를 제외하고는 id가 null인 경우가 없다.

그런데, Entity를 새로 생성한 후 DB에 저장할 때 이 getter로 인해 NPE가 발생했다.

<br>

JPA는 reflection으로 멤버변수를 읽기 때문에 getter를 호출하지 않는 줄 알고 있었는데 왜??

이유를 찾아보려 CrudRepository의 save 코드를 따라가봤더니 어느 지점에서 getter를 호출하고 NPE가 발생하는 걸 확인했다.

<br>

당연한 얘기지만 getter의 리턴 타입은 필드 타입과 일치시켜주어야 한다.

위 경우에서는 아예 getter가 없는 경우에도 잘 동작한다. (이로 인해 JPA는 getter가 아니라 직접 멤버 변수에 접근한다는 걸 알 수 있다.)

<br>

아래는 내가 남긴 커밋 로그

> Fix bug that NPE occures when JPA try to save Entity
>
> id 타입이 Long(Wrapper)이지만 getId의 return 타입이 long(Primitive type) 이라서 발생하는 버그였습니다.
> 1. Answer나 Question을 새로 생성할 때 id 멤버변수는 null값을 가집니다.
> 2. CrudRepository가 save하기 위해 getter를 호출할 때 Long인 id를 long으로 unboxing 합니다.
> 3. id가 null이므로 null pointer exception이 발생합니다.
>
> JPA는 reflection로 멤버 변수를 접근하여 DB에 반영하는 걸로 알고 있습니다.
> CrudRepository의 save 메서드에서 getter를 호출하기 때문인 것 같습니다.

