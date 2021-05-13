---
layout: post
title:  "Optional - orElse 와 orElseGet의 차이"
excerpt: ""

categories:
  - Java
tags:
  - [Java]

toc: true
toc_sticky: true
comments: true
 
date: 2021-05-13
last_modified_at: 2021-05-13
---
## orElse 와 orElseGet를 똑같다고 생각하면 에러가 발생할 수 있다.

그 이유가 뭔지 알아보자.

orElse와 orElseGet 모두 Optional의 객체가 널일 경우 반환해야 할 값을 정의한다.

다만 차이점은 orElse는 값을 받고 orElseGet은 Supplier를 받는다.

여기서 네이밍의 이유를 알게 된다.

Supplier 함수형 인터페이스는 get() 메소드로 값을 불러오기 때문에,

orElseGet 은 null일 경우 supplier의 get() 값을 반환하라는 뜻인것이다.

(orElse는 null일 경우 그냥 값을 반환하라는 뜻)

<br>

각각 코드를 보면 다음과 같다.

```java
    public T orElse(T other) {
        return value != null ? value : other;
    }
```

```java
    public T orElseGet(Supplier<? extends T> other) {
        return value != null ? value : other.get();
    }
```

둘다 value가 null일 경우 값을 반환하는데 차이가 뭘까?

바로 값을 바로 파라미터로 받느냐, Supplier를 파라미터로 받느냐의 차이이다.

아래 예시를 보면 더 이해가 될 것이다.

```java
    public User findUserOrCreate(String email, ResourceServer resourceServer) {
        return userRepository.findByEmail(email).orElse(createUser(email, resourceServer));
    }

    private User createUser(String email, ResourceServer resourceServer) {
        return userRepository.save(new User(email, resourceServer));
    }
```

findUserOrCreate 메소드의 의도는 email로 유저를 찾았다가, 유저가 없다면 새로 유저를 등록하고 반환하라는 것이다.

여기서 문제가 발생하는데, orElse는 값을 파라미터로 받는다.

<br>

orElse를 풀어서 생각하면 아래와 같은 플로우가 될 것이다.

```java
Optional<User> userOptional = userRepository.findByEmail(email);
User newUser = createUser(email, resourceServer);
User user;
if(userOptional.isPresent){
	user = userOptional.get();
} else {
	user = newUser;
}
```

따라서, orElse는 값이 null이든 아니든 createUser를 실행하게 된다.

<br>

그렇게 되면 user테이블에 email이 unique가 걸려있으면 에러가 발생하고, 

unique가 걸려있지 않아도 같은 email의 유저가 매번 생기기 때문에 둘다 문제가 발생한다.

<br>

한편 orElseGet은 Supplier를 파라미터로 넘기기 때문에

null이 아니면 메소드가 실행되지 않고 원하는 동작이 잘 실행된다.

orElseGet를 풀어쓰면 다음과 같은 플로우가 될것이다.

```java
Optional<User> userOptional = userRepository.findByEmail(email);
Supplier<User> supplier = () -> createUser(email, resourceServer);
User user;
if(userOptional.isPresent){
	user = userOptional.get();
} else {
	user = supplier.get();
}
```

보는 바와 같이 null이 아닐 경우에만 .get()을 실행해서 이름이 orElseGet 인 것이다.

get()을 실행하면 내부에 있는 createUser가 실행해서 반환할 것이다.

즉, null일 경우에만 createUser가 실행되는 것이다.

<br>

## 요약

Optional의 value가 null일 경우에만 어떤 메소드를 실행해서 값을 반환하고 싶으면

(null이 아닐 땐 메소드를 실행하기 싫다면)

orElse 대신 orElseGet을 사용하자.

