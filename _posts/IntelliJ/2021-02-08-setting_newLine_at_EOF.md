---
layout: post
title:  "intelliJ에서 Test 코드 generate시 EOF에 new line 자동추가"
excerpt: ""

categories:
  - intelliJ
tags:
  - [intelliJ, ]

toc: true
toc_sticky: true
comments: true
 
date: 2021-02-09
last_modified_at: 2021-02-09

---

코드스쿼드 코드리뷰 중 JUnit test 클래스에서 EOF에 newLine을 넣지않은 실수를 했다.

command + n 을 통해 자동 generate시 자동으로 newLine을 넣어주도록 설정하는 법을 기록해놓는다.

## 자동으로 newLine을 넣는 방법

## No newline at end of file

`intelliJ` 에서는 `커맨드` + `N` 단축키를 이용하여 테스트 클래스를 자동생성할 수 있는데, 이 기능을 그냥 이용하게 되면 거슬리는게 하나 있다.
![test_gen](https://user-images.githubusercontent.com/25237661/79998744-0b9ed000-84f6-11ea-845c-b0e5f621453b.gif)

**소스트리**
![image](https://user-images.githubusercontent.com/25237661/80000609-4f92d480-84f8-11ea-81b9-685007f3347f.png)

**깃헙**
![image](https://user-images.githubusercontent.com/25237661/80001543-73a2e580-84f9-11ea-9614-1f321e55f53a.png)

**터미널**
![image](https://user-images.githubusercontent.com/25237661/79999509-f37b8080-84f6-11ea-9b31-2604fd811578.png)

EOF 전에 공백 라인이 없어서 생기는 문제인데, **마지막 라인에 공백라인 하나만 추가**하면 해결된다.

그런데 테스트 코드를 생성할 때 마다 공백라인을 추가하는 행위를 하자니 너무 원시적이며, 되게 자주 까먹는다.

> PR 2번중에 1번꼴로 하는 실수였다. 이렇게 실수를 자주하다가 현타가와서 도구를 활용하기로 했다.

더블 쉬프트 후 `file and code templates` 입력
![image](https://user-images.githubusercontent.com/25237661/80002548-bfa25a00-84fa-11ea-866a-9af093ce17d1.png)

여기에서 자주 사용하는 테스트 도구를 선택
![image](https://user-images.githubusercontent.com/25237661/80002583-caf58580-84fa-11ea-8bc9-5889d91a3471.png)

공백 라인을 추가한다.
![image](https://user-images.githubusercontent.com/25237661/80002993-596a0700-84fb-11ea-9173-b05e93c795cf.png)

<br>

-출처: [https://jehuipark.github.io/note/use-intellij-more-well](https://jehuipark.github.io/note/use-intellij-more-well)

