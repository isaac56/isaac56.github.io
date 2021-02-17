---
layout: post
title:  "Scanner 사용 시 close해야 하는 이유"
excerpt: ""

categories:
  - Java
tags:
  - [Java]

toc: true
toc_sticky: true
comments: true
 
date: 2021-02-17
last_modified_at: 2021-02-17

---

Scanner의 경우 외부 리소스 (I/O) 이므로, 사용이 끝나면 항상 close를 통해 반환해주어야 한다.

Scanner 뿐만 아니라 모든 외부 리소스는 사용 후 반환해주어야 하는데, 그 이유는 다음과 같다.

- 다른 프로세스 또는 쓰레드에서 사용을 못할 수 있다.
- DB의 경우 connection pool이 꽉 차면 마찬가지로 다른 프로세스 또는 쓰레드에서 사용을 못한다.

그래서 외부 리소스는 항상 반환해주는 습관을 갖자.



Java에서 [try-with-resources](https://www.baeldung.com/java-try-with-resources)를 사용하면 블럭이 끝났을 때 자동으로 리소스를 반환해주므로 유용하다.

