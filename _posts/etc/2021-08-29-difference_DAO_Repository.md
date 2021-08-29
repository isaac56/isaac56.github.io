---
layout: post
title:  "DAO와 Repository의 차이점"
excerpt: ""

categories:
  - etc
tags:
  - [DDD, Design Pattern]

toc: true
toc_sticky: true
comments: true
 
date: 2021-08-29
last_modified_at: 2021-08-29
---

## 계기

지금까지 개인프로젝트에서는 jpa를 사용했었고 자연스럽게 DAL(Data Access Layer)로 Repository pattern을 사용하고 있었다.

새로 입사한 회사에서는 mybatis를 사용하고, Repository가 아니라 DAO를 주로 사용하고 있는데

지금까지 Repository나 DAO나 모두 DB에 접근해서 Data를 가져오는 이름만 다르고 기능은 같은 pattern이라고 생각하고 있었다.

> 아무 생각 없이 "mybatis는 레거시고, jpa가 요즘 기술이니까 짱이야!" 라고 생각했었는데, 모두 각자의 사정이 있는 것 같다.
>
> 현직장은 여러 테이블 join을 통해 가져올 데이터가 많고, 회사에 DBA가 존재하여 모든 쿼리의 검수를 받게 되어 있다.
>
> 1. 외래키로 연결되어 있지 않은 경우 jpa의 연관관계로 가져올 수 없고, N+1쿼리 문제 등 jpa를 잘 알지 못하면 문제가 발생할 수 있다.
>
> 2. 1번 문제는 사실 jpa를 공부하면 되는 문제이지만, 애초에 기존 DB 구조가 외래키로 잘 연결되어 있지 않다.
>
>    (수천만이 사용하는 실서비스 DB는 이런 구조를 뜯어고치기 불가능에 가까운 것 같다)
>
> 3. 이게 제일 큰 이유인 것 같은데, 매번 DBA에게 쿼리 검수를 받아야 하는 상황에서 JPA를 사용하면 직접 sql을 추출해야 한다. 
>
>    코드가 같아도, 상황에 따라 sql이 달라질 수도 있으니 꽤 오버헤드가 큰 것 같다.

<br>

문제는 최근에 생기게 되었다.

예를 들자면, ``사람``이라는 테이블에 ``성별`` 값을 pk로 추가해야 하는 상황이 생겼다.

``사람``은 tps가 50이 넘고 수천만 row가 존재하는 테이블이라, pk를 변경할 경우 table lock이 10분정도 발생할 수 있다.

> mysql은 clustered index 기반의 rdb이기 때문에, pk 변경을 할 때 모든 데이터를 재정렬해야 한다.
>
> 또한 pk의 구성칼럼이 많아질수록 성능에 좋지 않은 영향이 가게 된다.

10분 장애를 발생시킬 수는 없으니, migration후 rename 방법을 생각 했으나 이 또한 상당히 복잡하고 일정내에 완료하기 힘들.

결국 테이블 분리를 하여 데이터를 관리하게 되었다. (``여자사람``, ``남자사람``)

<br>

분리한 코드에 따라 DAO를 작성하게 되었다.

테이블 분리는 DB의 문제이고, WAS에서는 ``사람`` 이라는 class로 다루고 있으니 ``사람`` 에 대한 DAO를 만들고, DAO 내부에서 ``여자사람``,``남자사람`` 테이블에 대한 sql을 조합하여 ``사람`` 이라는 테이블 하나를 다루는 것처럼 DAO 인터페이스를 만들었다.

``DAO는 테이블 당 하나를 만들어야 하는 규칙에 위반되고, DAO내부의 로직이 있는게 맞는지 고민해보세요`` 라는 리뷰를 받았다.

Repository처럼 생각하고 코드를 작성했던 것 같다.

그러다 보니, DAO도 테이블보다 Entity 중심(java bean)으로 설계하면 되지 않나 라고 생각하게 되었다.

(JPA는 Entity가 Table과 일치할 수 밖에 없으니 위와 같은 일(두개의 테이블 조합해서 하나의 Entity를 다루는 것처럼 속이기)이 발생할 일이 없다.)

이번 리뷰를 통해 DAO와 Repository의 차이를 한번 짚고 넘어가보려고 한다.

## DAO Pattern

DAO(Data Access Object) 패턴은 Data Persistence의 추상화이며, Application code보다는 데이터 저장소에 더 가까운 것으로 간주 된다.

따라서 많은 경우에 DAO는 데이터베이스 테이블과 일치하게 된다.

데이터를 보내고 검색하는 등의 간단한 메소드를 노출하고, 수행하기 위한 복잡한 쿼리는 내부적으로 감추고 있다.

<br>

## Repository Pattern

 Eric Evans의 Domain-Driven Design에 따르면, "repository는 객체의 Collection을 저장, 검색 하는 등의 동작을 캡슐화 한다" 고 한다. 여기서 Collection은 java의 Collection class를 말한다기보다 객체의 모임(저장 검색 등이 가능한)이라고 생각하면 될 것 같다.

마찬가지로, Patterns Of Enterprise Application Architecture에 따르면, "Domain 객체에 접근하기 위해 Collection과 같은 인터페이스를 사용하여, 도메인과 Data Mapping Layer 사이를 중재한다" 라고 정의하고 있다.

언뜻 보면, Repository는 data를 다루고 내부적으로 sql을 감추고 있다는 점에서 DAO와 같다고 착각할 수 있다.

그러나 Repository는 객체의 Collection 을 다루듯이 캡슐화, 즉 인터페이스를 제공한다는 점에서 DB 테이블보다 객체 중심의 Layer라고 할 수 있다.

Repository로 다루는 객체를 다루기 위해서 여러 테이블에 접근해야 한다면, 여러 DAO를 사용할 수 있는 것이다.

<br>

## 결론

Data Access Layer라는 점에서 Repository와 DAO는 공통점을 갖지만, Repository는 객체 중심, DAO는 데이터 저장소(DB 테이블) 중심인 것이다.

또한, Repository는 객체 중심으로 데이터를 다루기 위해 하나 이상의 DAO를 사용할 수 있으며, 따라서 DAO보다 higher layer이다.

<br>

JPA는 Entity를 정의하고, Repository interface를 작성하면 직접 구현체를 작성할 일이 없기 때문에 이러한 개념에 무지했던 것 같다.

