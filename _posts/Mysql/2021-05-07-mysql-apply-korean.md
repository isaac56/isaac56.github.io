---
layout: post
title:  "Mysql 한글 깨짐 문제"
excerpt: ""

categories:
  - Mysql
tags:
  - [Mysql]

toc: true
toc_sticky: true
comments: true
 
date: 2021-05-07
last_modified_at: 2021-05-07

---

### AWS EC2에 MYSQL 설치하고 spring boot 연결까지 했는데, 한글이 ???로 들어간다

기본적으로 MYSQL의 character set이 latin1으로 되어 있기 때문에 한글을 인식할 수 없다.

### 설정 파일에 들어가서 character set을 UTF8로 변경한 후 재시작하자

- mysql 설정 파일에 들어가서 아래 내용을 추가한다.

```
[mysql]
default-character-set = utf8

[client]
default-character-set = utf8

[mysqld]
character-set-server = utf8
collation-server = utf8_general_ci
init_connect=’SET NAMES utf8’
```

- mysql 데몬을 재시작 한다.

```
sudo systemctl restart mysqld
```

- 이미 테이블을 생성한 적 있으면 해당 테이블은 생성했을 당시의 character-set으로 남아있다.
  - 테이블을 새로 생성하거나, character set을 바꾸자
  
  - 테이블의 character set 확인
  
    ```
    SHOW CREATE TABLE table_name; 
    ```
  
  - 테이블의 character set 변경
  
    ```
    ALTER TABLE table_name CONVERT TO character SET utf8;
    ```
  
    



