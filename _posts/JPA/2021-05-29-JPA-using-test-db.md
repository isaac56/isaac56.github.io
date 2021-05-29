---
layout: post
title:  "JPA - repository test에서 테스트용 디비 사용하기"
excerpt: ""

categories:
  - JPA, DataJpaTest
tags:
  - [Spring-boot,JPA,DataJpaTest]

toc: true
toc_sticky: true
comments: true
 
date: 2021-05-29
last_modified_at: 2021-05-29
---

## Repository의 unit test를 진행할 때, 디비의 저장된 데이터의 영향을 받을 수 있다.

그렇다면 BeforeEach에서 관련 table을 싹 지운다음에 원하는 db 상황을 만들고 rollback을 하면 되지않을까?

test class 에 @Transactional 어노테이션이 붙으면 각 단위 테스트가 끝나면 자동으로 rollback이 된다.

아래와 같이 테스트를 수행하면 rollback이 수행되어 db를 조작한 내용이 실제 db에 commit되지 않는다.

(@DataJpaTest 자체에 @Transactional이 붙어 있다.)

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class AccommodationRepositoryTest {
    private UserRepository userRepository;
    private AccommodationRepository accommodationRepository;

    @Autowired
    public AccommodationRepositoryTest(UserRepository userRepository, AccommodationRepository accommodationRepository) {
        this.userRepository = userRepository;
        this.accommodationRepository = accommodationRepository;
    }

    @BeforeEach
    void setUp() {
        //관련 테이블 싹 날리기
        userRepository.deleteAll();;
        accommodationRepository.deleteAll();
        
        //원하는 db 상황 만들기
        //...
    }
  	
  	//test 코드 작성
}
```



그러나 일반적으로 fk로 얽혀있는게 많기 때문에 deleteAll로 쉽게 지울 수도 없고, 

아무리 rollback한다고 해도 실제 사용중인 db 테이블을 테스트 할 때마다 전부 날리는 행위는 매우 좋지 않은 것 같다.



## @DataJpaTest를 사용하여 테스트용 DB 사용하기

@DataJpaTest와 @AutoConfigureTestDatabase를 사용하여 테스트 시에만 특정 DB를 사용할 수 있다.

예를 들어, 상용 DB는 mysql을 사용하다가 테스트 시에만 Embeded db를 사용하는게 가능하다.

@AutoConfigureTestDatabase 의 replace 값을 ANY로 설정하고(default가 ANY라 따로 건들 필요는 없다),

connection 값을 원하는 Embedded로 바꾸고 실행하면된다.

(코드를 찾아보니 사용할 수 있는 DB는 H2, Derby, HSQL 이 있는 것 같다. 사용하려는 db driver의 gradle 의존성 추가는 필수)

```java
@DataJpaTest
@AutoConfigureTestDatabase(connection = EmbeddedDatabaseConnection.H2)
class AccommodationRepositoryTest
```



그러나, 상용 DB와 Embedded DB가 다르기 때문에 DDL 문이 에러가 날수도 있다.

DDL이 에러가 난다면, ddl-auto를 사용해 Embedded DB 맞는 DDL을 생성하여 초기화할 수도 있는데,

이때는 test 할 때마다 application.properties에 ``spring.jpa.hibernate.ddl-auto`` 을 바꿔야하는 번거로움이 있다.

그리고 상용 DB의 특정 기능을 사용하고 있는 경우, DB 자체가 다르기 때문에 ddl-auto를 사용하여도 에러가 날 수 있다.



## @TestPropertySource를 사용하여 테스트용 DB 사용하기

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@TestPropertySource("classpath:application-test.properties")
class AccommodationRepositoryTest
```

위와 같이, @TestPropertySource에 test용 application-test.properties 경로를 추가한다.

> @AutoConfigTestDatabase의 replace값을 NONE으로 해야지만 DataSource에 설정된 DB로 연결된다.
>
> replace 값은 Default가 ANY이고, ANY일 경우엔 @AutoConfigTestDatabase의 connection 값에 해당하는
>
> Embedded DB로 연결하려고 한다.
>
> 그리고 connection값이 설정되지 않으면 에러가 나는 것이다.
>
> java.lang.IllegalStateException: Failed to replace DataSource with an embedded database for tests. If you want an embedded database please put a supported one on the classpath or tune the replace attribute of @AutoConfigureTestDatabase.

그리고 해당 위치에 application-test.properties를 같은 mysql이지만 test용 db에 연결하도록 설정하면 된다.

나는 이 방법이 제일 편리한 것같다.

application-test.properties의 dataSource를 test용 DB 주소로 연결하고, 

spring.datasource.initialization-mode=always로 설정하면 항상 test DB를 초기화하면서 테스트를 할 수 있기 때문이다.

즉, 기존 데이터가 테스트에 영향을 주지 않는 것이다.

그리고 상용 DB와 같은 종류의 DB로 연결할 수 있기 때문에 Embedded DB를 사용할 때 발생하는 에러가 나지 않는다.

