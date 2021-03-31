---
layout: post
title:  "Spring-boot 실행 시 DNS Look up 없이 실행하기(Mac)"
excerpt: ""

categories:
  - Spring-boot
tags:
  - [Spring-boot, Mac]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-03
last_modified_at: 2021-03-03
---

## mac 환경에서 spring-boot 프로젝트 실행 시 30초나 걸렸다.

```
2021-03-03 15:05:00.292  WARN 2590 --- [  restartedMain] o.s.boot.StartupInfoLogger               : InetAddress.getLocalHost().getHostName() took 5002 milliseconds to respond. Please verify your network configuration (macOS machines may need to add entries to /etc/hosts).
```

위와 같은 메세지가 원인인 것 같아 구글링을 한 결과, /etc/hosts 파일을 수정함으로써 해결할 수 있었다.

sudo vi etc/hosts 명령어로 hosts 파일을 열어서 아래와 같이 내용을 추가한다.

```
127.0.0.1   localhost [터미널에서 hostname 명령으로 나오는 이름]
::1         localhost [터미널에서 hostname 명령으로 나오는 이름]
```

<br>

stackoverflow 답변을 보면, 

30초나 걸렸던 이유는 내 DNS 서버 설정이 잘못되어서 DNS 확인 시도가 실패했기 때문일 것이라고 한다(DNS 확인 timeout이 30초인가 보다)

위와 같이 설정하면 빨라지는 이유는, 호스트 파일에 항목을 추가하면 호스트 이름을 로컬에서 확인할 수 있으므로 실제 (원격) DNS 서버에 대해 호스트 이름을 확인하려는 시도를 건너 뛰기 때문이라고 한다.

<br>

[https://stackoverflow.com/questions/33289695/inetaddress-getlocalhost-slow-to-run-30-seconds](https://stackoverflow.com/questions/33289695/inetaddress-getlocalhost-slow-to-run-30-seconds)