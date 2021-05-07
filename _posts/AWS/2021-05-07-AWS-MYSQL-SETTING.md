---
layout: post
title:  "AWS EC2 에 mysql 세팅하기 (amazon linux)"
excerpt: ""

categories:
  - AWS
tags:
  - [AWS, Mysql]

toc: true
toc_sticky: true
comments: true
 
date: 2021-05-07
last_modified_at: 2021-05-07

---

## 1. AWS 에 가입해서 로그인

각자 가입할 수 있다고 생각한다.

## 2. 콘솔에 접속해 인스턴스 생성

AWS 메인 페이지 상단 오른쪽에 콘솔에 로그인 버튼을 클릭해 콘솔 페이지로 이동한다.
![img](https://media.vlpt.us/images/sechawang/post/d5163c06-4132-4543-bc9a-c151be8b9732/image.png)

전체 서비스 - 컴퓨팅 - EC2 를 선택한다.
![img](https://media.vlpt.us/images/sechawang/post/19d5dd19-5718-4f79-82a4-ecda3e7abc26/image.png)

왼쪽 메뉴에서 인스턴스 - 인스턴스 를 클릭한다.

상단에 파란색 인스턴스 시작 버튼을 클릭한다.

가장 상단에 Amazon Linux 를 선택한다.
![img](https://media.vlpt.us/images/sechawang/post/a52993f4-9e29-406e-a1fc-11f07a74025e/image.png)

원하는 사양을 선택 후 검토, 다음을 누르면 위와 같이 키 페어를 선택하는 페이지가 뜬다.
![img](https://media.vlpt.us/images/sechawang/post/8f71468a-af06-4909-8458-6439d247542a/image.png)

서버에 접속하기 위해 필요한 파일로 중요한 파일이다.

기존에 만든 적이 없다거나 뭔지 잘 모르겠다면 "새 키 페어 생성" 을 선택한 후 원하는 키 페어 이름을 작성하고 키 페어 다운로드 버튼을 클릭해서 키 페어 파일을 다운로드 받는다.

키 페어 파일을 다운로드 받으면 인스턴스 시작 버튼을 누를 수 있게 되며 인스턴스 시작을 눌러 인스턴스 생성을 완료한다.

터미널로 인스턴스에 접속
ssh -i <인증서이름 경로, 이름>.pem ec2-user@<서버IP>

로 접속해주면 된다.

맥의 경우 다운로드 받은 인증서는 Downloads 폴더에 가면 있다.

터미널을 연 뒤에 Downloads 폴더로 이동하고 위에 명령어로 접속하면 접속이 된다.

![img](https://media.vlpt.us/images/sechawang/post/ada0825c-1120-432e-950b-4a84beb82e73/image.png)

## 3. MySQL 5.7 설치

sudo yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm

sudo yum -y install mysql-community-server

위에 두 명령어를 차례로 복사 붙여넣기 해서 실행하면 MySQL 이 설치된다.

sudo systemctl start mysqld

이 명령어를 이용해서 mysql 데몬을 실행한다.

sudo systemctl enable mysqld

이 명령어를 이용해서 재부팅시 mysqld 가 자동으로 시작되도록 설정한다.

sudo netstat -plntu

를 사용해서 현재 3306 포트로 가 열려있는지 확인한다.

![img](https://media.vlpt.us/images/sechawang/post/e8bde7a1-203d-4616-a970-f0308fcf99bd/image.png)

만약 3306 이 안보인다면 AWS 보안그룹에 3306번 포트를 열고

sudo systemctl restart mysqld

명령어로 mysqld 를 재실행한다.

## 4. MySQL 루트 패스워드 설정

sudo grep 'temporary' /var/log/mysqld.log

명령어를 실행하면 기본 설정된 패스워드가 보여진다.
![img](https://media.vlpt.us/images/sechawang/post/af90b828-8a98-45b7-a4ca-21b057381bef/image.png)

나의 경우 rd?hY2)STqaW 였다.

이 부분을 복사한다.

mysql -u root -p

명령어로 mysql 에 접속하며 패스워드는 위에서 복사한 기본 패스워드를 붙여넣기해서 로그인에 성공했다.

ALTER USER 'root'@'localhost' IDENTIFIED BY '신규암호';

명령어로 패스워드를 변경할 수 있다.

flush privileges;

반영하는 명령어이다.

위 두 명령어는 mysql 에 접속한 상태에서 실행해야 한다.

## 5. MySQL 백업 및 복구

기존 DB 가 있는 서버에서 아래 명령어를 실행해 DB 를 백업한다.

mysqldump --routines -uroot -p"디비패스워드" "백업할DB" > "파일명".sql

"한글" 은 적절히 영문으로 변경해서 넣으면 된다.

ex)

mysqldump --routines -uroot -pabc123 real > real20201020.sql

그 뒤에 생성된 real20201020.sql 을 FTP 등을 이용해서 새로 복구할 서버로 옮긴다.

새로 생성한 DB 서버에서 데이터베이스를 만들어주어야한다.

아까처럼 mysql -u root -p 명령어를 이용해서 mysql에 접속한 뒤에 아래 명령어로 데이터베이스를 만든다.

CREATE DATABASE <백업한 데이터베이스명>

그 뒤에 exit 로 빠져나가서 아래 명령어를 실행한다.

mysql -u유저이름 -p패스워드 DB명 < 백업파일명

## 6. 외부 IP 접속 허용

MySQL Workbench 등으로 접속해서 관리하거나 다른 소스에서 MySQL 에 접근해야 한다면 외부 IP 접속을 허용해야한다.

grant all privileges on *.* to root@'%' IDENTIFIED BY 'root 비밀번호';

flush privileges;

이 명령어를 mysql 에서 하면 허용이 된다.



## 원본

<https://velog.io/@sechawang/AWS-%EC%97%90-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%84%9C%EB%B2%84-%EC%85%8B%ED%8C%85%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95MySQL-5.7>

## 참고

https://blog.eomsh.com/25

https://fishpoint.tistory.com/1406

https://cloudjak.tistory.com/17