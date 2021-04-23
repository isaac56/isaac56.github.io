---
layout: post
title:  "Mysql의 스키마 설계시 status code의 처리"
excerpt: ""

categories:
  - Mysql
tags:
  - [Mysql]

toc: true
toc_sticky: true
comments: true
 
date: 2021-04-23
last_modified_at: 2021-04-23

---

## Mysql에서 테이블 설계 시 status code를 어떻게 처리하는게 좋을까?

예를 들어 다음과 같은 상황이 있다고 하자.

카센터의 ``정비차`` 목록을 테이블로 저장하려고 하고 스키마의 칼럼은 다음과 같다.

```
차량번호 | 정비상태 | 입고일
VARCHAR|   ?   | DATE
```

여기서 정비상태는 ``입고``, ``정비중``, ``정비완료`` 세가지라고 가정할 수 있다.

그렇다면 정비상태의 데이터 타입을 뭘로 하면 좋을까에 대한 고민이 생긴다.

이런 상황에서 정비상태와 같은 칼럼을 지칭하는 용어를 status code로 하기로 했다.



status code를 mysql로 저장할 방법에 대해서는 일단 세가지 방법이 떠오른다.

1. INT로 저장

   INT로 저장할 경우, 0=입고, 1=정비중, 2=정비완료 와 같이 저장할 수 있다.

   그러나, DB의 데이터만으로는 각 숫자가 무슨 뜻인지 알 수 없어서 매직넘버로 저장한다는 찝찝함이 남는다.

   <br>

2. VARCHAR으로 저장

   입고는 입고로, 정비중은 정비중, 정비완료는 정비완료로 저장할 수 있다.

   근데 status가 기껏해야 3개인데 모두 문자열로 저장하는건 디스크 낭비라는 찝찝함이 남는다.

   <br>

3. ENUM으로 저장

   mysql에서 지원하는 Enum타입을 만들어서 해당 타입으로 저장하는 방법이다.

   이 경우에는, 

   매직넘버의 문제도 없고 디스크 낭비라(mysql에서 enum타입은 내부적으로 int로 저장됨) 문제도 사라진다.

   그러나, 확장성에서 문제가 된다.

   가령, 잘 없겠지만 DB를 다른 종류로 migration하는 과정에서 enum 타입을 지원하지 않는 DB로 바꾼다면,

   문제가 될 수 있다.

   또, 위 상황에서 부품부족으로 정비불가 라는 status가 추가된다고 해도 문제이다.

   새로운 Enum 타입을 선언해야하고 저장되어 있던 모든 데이터를 수정해야하는 문제가 생긴다.

   <br>

## reference table (또는 lookup table) 을 이용하자.

아래와 같은 칼럼으로 새로운 table을 만들고 ``정비차``.``정비상태`` 를 만든 table의 foriegn key로 만들면 된다.

id | value

0  | 입고

1  | 정비중

2  | 정비완료

<br>

이렇게 하면, 매직넘버, 공간낭비, 확장성 등의 문제가 해결된다.



참고 - <https://stackoverflow.com/questions/26509801/mysql-enum-vs-int>

