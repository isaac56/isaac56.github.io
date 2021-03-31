---
layout: post
title:  "redirect, forward, 그냥 리턴의 차이"
excerpt: ""

categories:
  - Spring-boot
tags:
  - [Spring-boot]

toc: true
toc_sticky: true
comments: true
 
date: 2021-03-11
last_modified_at: 2021-03-11
---

return "user": Controller -> Model -> View

- Controller가 Model을 View에게 전달하여 View를 반환하는 것

forward /user: 

- 주체 서블릿, 스프링이 user 컨트롤러를 호출한다.
- 코딩으로 한다면 userController의 메소드 직접 호출과 유사
- 이런것보다는 복잡한 로직은 서비스 계층을 만들어서 해결하자!

"redirect:/user" 

- 브라우저에게 해당 주소로 Get요청을 보내 접속하라고 시키는 것이다.
- response로 301(permently moved), 302(temporary moved) 보낼 수 있다.
- 301로 보낼 경우 브라우저에 캐싱되어 다음부터 자동으로 redirect 주소로 접속할 수 있다.