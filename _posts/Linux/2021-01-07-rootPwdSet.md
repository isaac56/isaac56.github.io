---
layout: post
title:  "su: Authentication failure 발생하는 이유"
excerpt: ""

categories:
  - Linux
tags:
  - [Linux]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-07
last_modified_at: 2021-01-07
---
# Root 비번 설정

- ubuntu를 깔고 사용자 등록 후 su 명령어를 통해 root로 전환하려는데 에러가 났다.

  ```bash
  su : Authentication failure
  ```

  - 나의 경우 root 비밀번호 설정을 하지 않아서 나는 에러였다.

  - root 비밀번호를 설정한다.

    ```bash
    sudo passwd root
    ```

    

