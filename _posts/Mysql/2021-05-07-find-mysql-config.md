---
layout: post
title:  "mysql 설정파일 경로찾기"
excerpt: ""

categories:
  - Mysql
tags:
  - [Mysql]

toc: true
toc_sticky: true
comments: true
 
date: 2021-05-07
last_modified_at: 2021-05-07

---

Mysql을 설치하는 OS마다 설정파일의 위치가 조금씩 달라 헷갈리는 경우가 많았다.

아래 커맨드를 사용하면 현재 mysql이 참고하고있는 설정파일의 위치를 알 수 있다.

```mysqld --verbose --help | grep -A 1 'Default options'```

