---
layout: post
title:  "리눅스 group이란, group 변경"
excerpt: ""

categories:
  - Linux
tags:
  - [Linux, group]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-08
last_modified_at: 2021-01-08
---
## 리눅스에서 group이란

리눅스에선 group이라는 것이 있는데, 어떤 파일이나 폴더를 특정 User에게만 공유하고 싶을 때 사용한다.

- root 디렉터리에서 ls -l 커맨드를 치면 아래와 같은 형식으로 폴더 및 파일 정보가 뜬다. 

```
drwxr-xr-x  93 root root     4096  Jan  8 01:25 etc
```
  1.  ```rwxr-xr-x``` 는 etc라는 폴더에 접근 권한이다.
  2. 3글자씩 잘라서, 소유자/그룹/타인 에 대한 접근 권한이다.
  3. ```root root``` 는 소유자/그룹 이 누구인지를 뜻한다.

이 때, group1이라는 그룹에 속한 사용자들에게만 권한을 주고 싶다면,

해당 폴더의 그룹을 group1으로 바꾸고 그룹에 대한 접근 권한을 설정해주면 된다.



### 현재 생성되어 있는 Group 확인

현재 생성되어 있는 group은 /etc/group 파일에서 확인할 수 있다.

### 특정 유저의 그룹 확인

```
#id user1
```

### 그룹 생성하기

```
#groupadd -g 777 group1
```

- GID가 777인 그룹 group1을 만든다.

### 유저를 그룹에 가입시키기

```
#usermod -G group1 user1
```

- user1을 group1에 가입시킨다.
- user1이 세 개 이상 그룹에 가입하기 위해서는 /etc/group을 수정해야함

### 그룹 삭제하기

```
#groupdel group1
```

- group1을 삭제 시킨다.
- group1을 기본그룹으로 하는 user가 있다면 삭제가 안됨

