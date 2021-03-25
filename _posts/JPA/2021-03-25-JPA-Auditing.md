---
layout: post
title:  "JPA Auditing을 이용하여 Entity에 생성시간, 수정시간 자동 부여"
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

Entity에 정의된 LocalDateTime 변수에 @CreatedDate, @LastModifiedDate등의 Annotation을 정의하면,

자동으로 Entity가 생성된 시간이나, 마지막으로 수정된 시간등이 저장된다.

```java
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public class AbstractEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @JsonProperty
    private Long id;

    @Column(nullable = true)
    @CreatedDate
    private LocalDateTime createdDateTime;

    @Column(nullable = true)
    @LastModifiedDate
    private LocalDateTime modifiedDateTime;
}    
```

다만, @CreatedDate와 같은 Annotation을 받아들여 처리할 수 있도록 

클래스에 @EntityListeners(AuditingEntityListener.class) 어노테이션을 붙여야 한다.

또한, Configuration 클래스에 @EnableJpaAuditing 어노테이션을 붙여야 한다.

```java
@SpringBootApplication
@EnableJpaAuditing
public class QnaApplication {
	public static void main(String[] args) {
		SpringApplication.run(QnaApplication.class, args);
	}
}
```

<br>

참고: <https://webcoding-start.tistory.com/53>

