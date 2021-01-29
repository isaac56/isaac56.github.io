---
layout: post
title:  "Load Data (bulk insert) 시도 secure_file_priv 문제 뜰 때"
excerpt: ""

categories:
  - Mysql
tags:
  - [Mysql]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-29
last_modified_at: 2021-01-29

---

## Mysql 에서 bulk insert 명령을 실행할 때, secure_file_priv 에러 발생

- 쉘스크립트에서 아래와 같이 특정 파일을 bulk insert시도 하였고 secure_file_priv에러 발생하였다.

- test.sh

  ```bash
  #!/bin/bash
  
  mysql -uroot -p패스워드 db명 << EOF
  
  LOAD DATA INFILE 'test.txt'
  INTO TABLE user_log
  FIELDS TERMINATED BY ',';
  EOF
  ```

  ```
  ERROR 1290 (HY000) at line 2: The MySQL server is running with the --secure-file-priv option so it cannot execute this statement
  ```

## 해결방법

- mysql에 접속하여 변수 상태를 확인한다.

```
mysql> SELECT @@GLOBAL.secure_file_priv;
+---------------------------+
| @@GLOBAL.secure_file_priv |
+---------------------------+
| /var/lib/mysql-files/     |
+---------------------------+
1 row in set (0.00 sec)
```

여기서 secure_file_priv에 설정된 경로에 파일이 위치해야만 bulk insert가 가능하다고 한다.

다만, mysql 설정 파일에 아래 내용을 추가한 후 mysql을 재시작하면 아무 위치에서나 bulk insert가 가능하다.

```
[mysqld]
secure-file-priv=""
```

