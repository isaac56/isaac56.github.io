---
layout: post
title:  "SSH root 권한으로 접속 허용하기"
excerpt: ""

categories:
  - Linux
tags:
  - [Linux, SSH]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-07
last_modified_at: 2021-01-07
---
## SSH root 권한으로 접속 허용하기

- SCP 명령어로 remote의 특정 폴더에 접근하려면 root권한이 필요할 때가 있다.

- 분명히 root 비밀번호가 맞았는데도 자꾸 Permission denied, please try again. 가 뜬다면 ssh 설정을 확인해야한다.

  1. remote(ubuntu 기준) 로 들어가서 /etc/ssh/sshd_config 파일을 연다

     ```bash
     vi /etc/ssh/sshd_config
     ```

  2. 파일 내의 PermitRootLogin 을 yes로 바꿔준다. (없으면 추가한다.)

     ```bash
     PermitRootLogin yes
     ```

  3. 데몬 명령어로 ssh를 재시작시켜 변경된 설정을 적용한다.

     ```bash
     systemctl restart ssh
     ```

     