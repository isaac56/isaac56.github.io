---
layout: post
title:  "SSH Agent Forwarding"
excerpt: ""

categories:
  - Linux
tags:
  - [Linux]

toc: true
toc_sticky: true
comments: true
 
date: 2021-05-04
last_modified_at: 2021-05-04

---

## SSH Key Forwarding을 사용하는 상황

일반적으로 어떤 서비스를 제공할 때 보안을 위하여 배포 DB는 사설 망에서 돌아가야 한다.

AWS 인스턴스를 만든다고 했을 때, VPC에 public 서브넷과 private 서브넷이 존재하고,

public 서브넷은 internet gateway 와 연결되어 있다.

여기서 public 서브넷에 연결되어 있는 인스턴스는 웹서버이며 22포트(SSH)와 80포트(웹서버)가 외부망에서 접근할 수 있다.

그리고 private 서브넷에 연결되어 있는 인스턴스는 DB 서버이며 22포트가 열려있지만 라우팅 테이블이 internet gateway에 연결되어 있지 않다.

따라서 DB서버는 외부에서 접근이 불가능하고 사설망에 존재하는 서버(동일 VPC내에 존재하는 서버)에서만 접근 가능하다.

즉, 내가 DB 서버에 접속하려면, 먼저 외부에서 접근 가능한 웹서버 인스턴스에 접속 후 웹서버 인스턴스에서 DB서버로 접속해야 한다.

나 ----(public ip로 ssh 접속)----> 웹서버 ----(private ip로 ssh 접속)----> DB서버

이때, 내 컴퓨터엔 웹서버에 SSH 접속하기 위한 private key와 DB 서버에 SSH 접속하기 위한 private key가 있다.

내 컴퓨터의 key를 이용해 웹서버에 ssh 접속했지만, 웹서버 내부엔 DB서버의 key가 없기 때문에 웹서버에서 DB서버로 접속은 할 수 없게 된다.

### 그렇다면 DB서버의 key를 웹 서버에 복사해놔야 할까?

그건 정말 바보 같은 짓이다.

DB서버의 보안이 중요하기 때문에 망 분리를 해놓은 것인데,

외부에서 접속가능한 웹서버에 DB서버 키를 보관한다면 망 분리한 이유가 없어진다.

웹 서버가 뚫리면 자동으로 DB서버도 뚫리기 때문이다.

### 그래서 사용하는것이 ssh agent forwarding이라고 한다.

내가 웹서버에 ssh 접속한 상태라고 가정해보자.

이 상태에서 DB서버에 접속을 시도하면, DB서버는 서버에 등록된 공개키로 어떤 문자열을 난수화 한 후 이걸 풀 수 있냐고 물어본다.(공개키와 쌍이 되는 개인키로 난수를 문자열로 해석 가능하다.)

보통은 현재 컴퓨터(웹서버)에 있는 암호키로 문자열을 풀지만,

ssh agent forwarding을 사용할 경우 다음과 같이 진행된다.

1. 난수를 내 컴퓨터(로컬)로 전달한다
2. 내 컴퓨터에 있는 개인키로 난수를 해석하여 문자열을 반환한다.
3. 웹서버에선 반환받은 문자열을 바로 DB서버로 보내어 인증을 완료한다.

이렇게 함으로써 개인키를 웹서버로 보내지 않고도 DB서버에 인증이 가능하다.

## 사용법

1. ssh-agent에 키 추가

   ```
   $ssh-add 키파일.pem
   ```

2. config에서 forwarding 허용

   ```
   $vi ~/.ssh/config
   
   //열린 파일에 아래와 같은 내용 추가
   Host {forwarding 허용할 remote의 IP주소}
   	ForwardAgent yes
   ```



- 참고자료 - <https://www.cloudsavvyit.com/25/what-is-ssh-agent-forwarding-and-how-do-you-use-it/>

