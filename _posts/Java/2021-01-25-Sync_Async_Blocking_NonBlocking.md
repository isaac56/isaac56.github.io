---
layout: post
title:  "Synchronous vs Asynchrnonous와 Blocking vs Non-Bolocking"
excerpt: ""

categories:
  - Java
tags:
  - [Synchronous, Asynchronous, Blocking, Non-Bolocking]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-25
last_modified_at: 2021-01-25
---
## Synchronous / Asynchronous

Synchronous의 어원부터 살펴보면, Sync (함께) + Chrono(시간) 이 합쳐진 단어이다.

즉, Synchronous란 '함께 시간을 맞춰 행동한다'고 해석할 수 있다.

<br>

예를 들어, B라는 function을 사용하는 A라는 function이 있다고 해보자

여기서 A는 호출한 자라고 할 수 있고, B는 호출받은 자라고 할 수 있다.

### Synchronous

A는 B를 호출하게 되고, B가 종료되는 시점에 A가 그 다음 동작을 수행한다면 Synchronous라고 할 수 있다.

호출된 자의 종료 시간과 호출한 자의 시작 시간이 일치하면 Synchronous인 것이다.

이렇게 시간을 맞춰 행동하면 코드의 작성 순서대로 실행된다는 것을 보장할 수 있게 된다.

```java
public void A(){
  System.out.println("A가 시작되었습니다.")
  B();
  System.out.println("A가 끝났습니다.")
}

public void B(){
	System.out.println("B입니다.")
}
```

자바는 기본적으로 Blocking / Synchronous한 언어이기 때문에, A() 메소드를 실행한다면

1. A가 시작되었습니다.
2. B입니다.
3. A가 끝났습니다.

순서로 출력이 될 것이다.

Synchronous하기 때문에, B의 호출이 끝나는 시간과 A가 시작되는 시간이 일치하기 때문이다.

다시 말하면, B의 호출이 끝난 후("B입니다." 출력) A가 다시 시작하여 ("A가 끝났습니다.")를 출력하기 때문이다.

<br>

### Asynchrnous

Asynchronous는 Synchronous에 반대를 뜻하는 접두사 A가 붙은 형태이다.

즉, 시간을 맞추지 않는다고 생각하면 될 것이다.

A가 B를 호출한 뒤, B의 종료여부를 신경쓰지 않고 다시 A가 작업을 재개하는 것을 생각하면된다.

호출된 자의 종료 시간과 호출한 자의 시작 시간이 일치하지 않는 것이다.

위의 예가 Asynchronous하게 동작한다면, 

A는 B를 호출한 후 B의 종료 시점을 기다리지 않고, "A가 끝났습니다."를 출력하려고 할 것이다.

따라서, "B입니다."가 먼저 출력될지 "A가 끝났습니다."가 먼저 출력될지 알 수 없게 된다.

코드의 작성 순서대로 실행되는 것이 보장되지 않는 것이다.

<br>

## Blocking / Non-Blocking

Blocking과 Non-Blocking을 설명하기 위해서 제어권이라는 용어가 많이 사용된다.

간단하게, 제어권이란 스스로 동작할 수 있는 권리라고 이해하면 된다.

A라는 함수에 제어권이 있다면 A는 현재 동작할 수 있는 상태인 것이다.

Blocking / Non-Blocking은 이런 제어권의 반환이 언제 되느냐에 대한 관점이다.

### Blocking

Blocking은 호출된 함수가 종료될 때까지 호출한 자에게 제어권을 넘겨주지 않는 것이다.

즉, 호출된 함수가 종료될 때까지 호출한 함수의 동작을 Block하는 것이다.

### Non-Blocking

Non-Blocking에서 호출된 함수는 제어권을 바로 호출한 함수에게 넘겨준다.

즉, 호출된 함수가 종료될 때까지 호출한 함수의 동작을 Block하지 않는다.

Non-Blocking 함수를 호출한다면, 호출자에게 제어권을 바로 반환하기 때문에,

호출자는 자신의 동작을 진행할 수 있고, 호출된 함수도 자신의 동작을 진행할 수 있다.

<br>

참고하기 매우 좋은 글

[https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/](https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/)

