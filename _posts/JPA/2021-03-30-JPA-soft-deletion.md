---
layout: post
title:  "JPA - soft deletion 쉽게 구현하기"
excerpt: ""

categories:
  - JPA
tags:
  - [Spring-boot,JPA]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-30
last_modified_at: 2021-03-30
---

## Soft Deletion

Soft deletion은 실제 데이터를 DB에서 삭제하지 않는다.

테이블에 deleted와 같은 상태 칼럼을 추가한 후, 삭제했으면 true 삭제하지 않았으면 false와 같이 flag를 표시하는 방법이다.

<br>

## JPA에서 쉽게 구현하기

### 삭제시 구문 대체하기

Entity class를 정의할 때 @SQLDelete 어노테이션을 통하면 쉽게 구현할 수 있다.

deleted라는 상태 칼럼을 갖는 User 테이블이 있다고 가정해보자.

```java
@Entity
@SQLDelete(sql = "UPDATE USER SET deleted=true where id=?")
public class User extends Entity{
    .....
}
```

위와 같이 정의하게되면, JPA에서 해당 엔티티의 delete 오퍼레이션을 수행할 때 어노테이션에서 정의한 sql문이 실행되게 된다.

### 클래스에 즉각 반영하기

JPA에서 delete를 수행하여 DB에서 deleted가 false로 수정되었다.

그러나 다시 DB에서 데이터를 가져오지 않는 이상, 서버의 Entity 인스턴스가 갖고 있는 deleted는 여전히 true일 것이다.

이러한 문제를 해결하기 위해서 @PreRemove 어노테이션을 사용할 수 있다.

```java
@Entity
@SQLDelete(sql = "UPDATE USER SET deleted=true where id=?")
public class User extends Entity{
    .....
    @PreRemove
    public void deleteUser(){
      this.deleted = false;
    }
}
```

@PreRemove가 붙은 메소드는 JPA가 delete를 수행하기 전에 수행된다.

### 살아있는 데이터만 가져오기

이렇게 해서 soft deletion을 구현하였지만, 매번 where deleted=true 를 통해 데이터를 가져오는건 번거롭다.

모두 수동으로 코드를 작성하면 삭제된 데이터가 보일 확률도 높다.

이땐 @Whare 어노테이션을 사용하면 쉽게 처리할 수 있다.

```java
@Entity
@SQLDelete(sql = "UPDATE USER SET deleted=true where id=?")
@Where(clause = "deleted=false")
public class User extends Entity{
    .....
    @PreRemove
    public void deleteUser(){
      this.deleted = false;
    }
}
```

@Where 어노테이션을 사용하면 JPA가 DB에서 해당 Entity를 검색할 때, 

@Where에 있는 조건에 해당하는 데이터만 불러온다.

<br>

출처 - <https://thorben-janssen.com/implement-soft-delete-hibernate/#Exclude_8220deleted8221_entities_in_queries>

