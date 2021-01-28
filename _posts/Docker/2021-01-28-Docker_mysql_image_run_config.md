---
layout: post
title:  "Docker mysql 이미지 실행 및 한글설정"
excerpt: ""

categories:
  - Docker
tags:
  - [Docker]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-28
last_modified_at: 2021-01-28

---

### Docker Docs를 참고하여 진행했습니다.

[Docker Docs](https://docs.docker.com/engine/reference/commandline/)

## mysql 이미지를 컨테이너로 실행하고 설정한 기록

1. docker hub 에서 mysql image pull 받기

   ```bash
   docker pull mysql:5.7
   ```

2. image를 container로 실행하기(-d는 background로 실행, -e는 환경변수 설정한다는 뜻)

   ```bash
   docker run --name mysql -e MYSQL_ROOT_PWD=비밀번호 -d mysql:5.7
   ```

3. 컨테이너에게 exec명령어로 bash 실행하기 (-i는 Keep STDIN open even if not attached라는 뜻, -t 는 psedo TTY를 할당한다)

   ```bash
   docer exec -it mysql /bin/bash
   ```

4. locale 설정하기 (Debian Linux 기준입니다.)

   ```bash
   apt update
   apt upgrade
   apt install -y locales locales-all
   dpkg-reconfigure locales #여기서 실행되는 프로그램에서 ko_KR.UTF-8을 선택
   ```

   4-1) 현재 컨테이너를 이미지로 만든 후 LC_ALL 환경변수를 ko_KR.UTF-8로 변경하여 재실행한다.

   ```bash
   docker commit mysql isaac/mysql
   docker container rm mysql
   docker run --name mysql -e LC_ALL=ko_KR.UTF-8 -d isaac/mysql
   ```

5. mysql -uroot -p비밀번호 명령어를 통해 mysql 접속한다.

   ```
   mysql -uroot -p루트비번
   ```

6. status 명령어로 현재 설정 확인하면 characterset이 latin1로 되어있는 걸 확인할 수 있다.

   ```
   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.33, for Linux (x86_64) using  EditLine wrapper
   
   Connection id:		3
   Current database:	
   Current user:		root@localhost
   SSL:			Not in use
   Current pager:		stdout
   Using outfile:		''
   Using delimiter:	;
   Server version:		5.7.33 MySQL Community Server (GPL)
   Protocol version:	10
   Connection:		Localhost via UNIX socket
   Server characterset:	latin1
   Db     characterset:	latin1
   Client characterset:	latin1
   Conn.  characterset:	latin1
   UNIX socket:		/var/run/mysqld/mysqld.sock
   Uptime:			49 min 29 sec
   
   Threads: 1  Questions: 7  Slow queries: 0  Opens: 105  Flush tables: 1  Open tables: 98  Queries per second avg: 0.002
   --------------
   ```
   
7. /etc/mysql/mysql.conf.d/mysqld.cnf 파일을 수정해준다. (도커허브 mysql:5.7 이미지 기준입니다.)

   ```
   //아래 내용을 파일에 추가해준다.
   [client]
   default-character-set=utf8
   
   [mysql]
   default-character-set=utf8
   
   [mysqld]
   collation-server = utf8_unicode_ci
   init-connect='SET NAMES utf8'
   character-set-server = utf8
   ```

8. docer container 명령어로 restart시켜준다

   ```
   docker container restart mysql
   ```

   

