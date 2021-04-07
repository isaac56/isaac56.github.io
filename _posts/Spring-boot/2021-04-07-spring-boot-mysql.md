---
layout: post
title:  "Spring-boot: mysql과 연동"
excerpt: ""

categories:
  - Spring-boot
tags:
  - [Spring-boot, mysql]

toc: true
toc_sticky: true
comments: true
 
date: 2021-04-07
last_modified_at: 2021-04-07
---

## spring-boot 프로젝트 mysql과 연동

Spring-boot는 간단한 설정만으로 mysql와 연동이 가능했다.

<br>

1. gradle.build에 dependency 설정

   - gradle.build 에 아래의 dependency를 추가한다.

     ```
     runtimeOnly 'mysql:mysql-connector-java'
     ```

2. application.properties에서 커넥션 정보 설정

  ```
  spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
  spring.datasource.url=jdbc:mysql://[IP주소]:[포트번호]/[db명]?serverTimezone=UTC&characterEncoding=UTF-8
  spring.datasource.username=[사용자]
  spring.datasource.password=[비밀번호]
  ```

  - com.mysql.jdbc.Driver 는 Deprecated이므로 com.mysql.cj.jdbc.Driver를 사용하자

3. 실행시켜서 정상 동작하는지 확인

