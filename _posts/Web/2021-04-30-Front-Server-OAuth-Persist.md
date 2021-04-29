---
layout: post
title:  "Front 서버와 REST API 서버로 나누어 배포할 때 OAuth 인증을 유지하는 이슈"
excerpt: ""

categories:
  - Web
tags:
  - [OAuth, Session]

toc: true
toc_sticky: true
comments: true
 
date: 2021-04-30
last_modified_at: 2021-04-30

---

## 초기 상황

1. REST API 서버에 OAuth 기능을 추가하였다.
2. call back url로 API서버를 등록하여, 깃헙 OAuth인증으로 발급받은 Access token을 API 서버의 세션에 저장한다.
3. 프론트 서버에서는 CORS error를 해결하기 위해 webpack-dev-server의 프록시 기능으로 API를 호출한다.
4. **세션 정보는 브라우저의 쿠키에 남아있지, 프론트 서버에는 없기 때문에 항상 Access token에 접근할 수 없다.**

## 프록시는 끄고 API 서버에서 Access-Control-Allow-Origin과 Access-Control-Allow-Credential를 설정

1. 프론트 서버의 webpack-dev-server 프록시 기능을 끄고, 브라우저에서 API서버로 직접 axios를 통해 가져오게 한다. (axios.defaults.withCredentials = true)

2. API 서버에서는 Access-Control-Allow-Origin을 프론트 서버로 설정하고, Access-Control-Allow-Credential을 true로 설정한다.

3. 이렇게 하게되면, 브라우저에서 쿠키를 담은 채로 API서버에 axios 요청하기 때문에 문제가 해결될 줄 알았다.(쿠키를 통해 세션을 확인하고, 세션에 access-token이 저장되어있기 때문에 로그인 되어있는지 확인 가능)

4. **로그인하면 반환되는 쿠키(세션 ID를 담은)는 [API 서버 주소]에 저장되는 쿠키이지, [프론트 서버 주소]에 저장되는 쿠키가 아니다..**

5. **따라서 프론트페이지(프론트서버주소)에서 아무리 api 서버에 요청을 해도 프론트페이지에 저장되어있는 쿠키가 없기 때문에 Access token을 확인할 수 없다.**

   >  (번외) 어떤 홈페이지에서 다른 도메인으로 api 요청을 했을때 오는 쿠키는 저장이 안된다.
   >
   > CSRF 공격을 막기 위해 Same Site on Cookie 룰이란게 존재하고,
   >
   > Response header에 sameSite 값을 지정하여 현재 반환된 쿠키를 어떤 상황에서 저장해도 되는지 알려준다.
   >
   > 크롬 80 부터는 LAX가 default 값이 되었다고하며, None일 경우에는 [브라우저가 접속중인 url]이 [Response 를 보낸 url]과 달라도 쿠키를 저장하게 된다. 
   >
   > - <https://tansfil.tistory.com/58>
   > - <https://ifuwanna.tistory.com/223>

## OAuth로 인증한 Access Token을 API 서버 세션에 저장한다는 것 자체가 잘못 된 것 같다.

- 찾아보니 JWT Token으로 저장하는 것 같은데 차후 구현해 보아야겠다.