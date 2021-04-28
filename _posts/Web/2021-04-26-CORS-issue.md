---
layout: post
title:  "CORS 이슈"
excerpt: ""

categories:
  - Web
tags:
  - [CORS]

toc: true
toc_sticky: true
comments: true
 
date: 2021-04-28
last_modified_at: 2021-04-28

---

프로젝트를 하다가 프론트 쪽에서 CORS 이슈가 있었고, 아래와 같이 해결하기로 하였다.

1. 프론트 측에서는 개발 시 webpack-dev-server 의 프록시를 사용하여 api서버에 요청을 보내어 CORS 이슈를 해결한다. (배포 시에는 webpack-dev-server의 NODE_ENV값을 배포로 바꾸어 프록시 기능을 끈다.)
2. 서버 측에서는 Access-Control-Allow-Origin 값을 웹서버 배포 리소스로 설정하여 배포한다.



CORS 이슈를 공부하며 좋은 자료를 찾았고, 이 자료를 통해 많은 걸 이해할 수 있게 되었다.

감사합니다 글 작성자님♥️ - <https://evan-moon.github.io/2020/05/21/about-cors/>

