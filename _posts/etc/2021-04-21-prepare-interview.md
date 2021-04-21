---
layout: post
title:  "한줄로 정리"
excerpt: ""

categories:
  - etc
tags:
  - [Interview]

toc: true
toc_sticky: true
comments: true
 
date: 2021-04-21
last_modified_at: 2021-04-21
---
Authentication(인증) - 내가 A라고 주장할 때, 실제로 내가 A인지 확인하는 것

Authorization(권한 부여) - 어떤 자원에 대한 접근을 허용해주는 것

<br>

## Oauth 2.0 (Open authentication)

Resource Owner: 일반적인 사용자

Client: 서비스 제공자 (ex: oauth를 통해 인증을 사용하고 싶은 서비스업체)

Authorization Server: 인증을 담당하는 서버 (ex: 깃헙 인증 서버)

Resource Server: 리소스를 가지고 있고 클라이언트에게 제공해주는 서버 (ex: 깃헙 데이터 서버)