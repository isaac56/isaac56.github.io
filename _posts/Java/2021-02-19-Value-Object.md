---
layout: post
title:  "VO(Value Object)"
excerpt: ""

categories:
  - Java
tags:
  - [Java]

toc: true
toc_sticky: true
comments: true
 
date: 2021-02-19
last_modified_at: 2021-02-19

---

> In [computer science](https://en.wikipedia.org/wiki/Computer_science), a **value object** is a small [object](https://en.wikipedia.org/wiki/Object_(computer_science)) that represents a *simple* entity whose equality is not based on identity: i.e. two value objects are *equal* when they *have* the same *value*, not necessarily being the *same object*.[[1\]](https://en.wikipedia.org/wiki/Value_object#cite_note-fowler-1)[[2\]](https://en.wikipedia.org/wiki/Value_object#cite_note-c2_vo-2)
>
> Examples of value objects are objects representing an amount of money or a date range.
>
> Being small, one can have multiple copies of the same value object that represent the same *entity*: it is often simpler to create a new object rather than rely on a single instance and use references to it.[[2\]](https://en.wikipedia.org/wiki/Value_object#cite_note-c2_vo-2)
>
> Value objects should be [immutable](https://en.wikipedia.org/wiki/Immutable_object):[[3\]](https://en.wikipedia.org/wiki/Value_object#cite_note-c2_vo_immutability-3) this is required for the implicit contract that two value objects created *equal*, should remain equal. It is also useful for value objects to be immutable, as client code cannot put the value object in an invalid state or introduce buggy behaviour after instantiation.[[4\]](https://en.wikipedia.org/wiki/Value_object#cite_note-4)
>
> Value objects are among the [building blocks of DDD](https://en.wikipedia.org/wiki/Domain-driven_design#Building_blocks).

위키피디아에서는 Value Object를 다음과 같이 정의하고 있다.

Value Object는 값을 나타내는 객체이고, 다음과 같은 속성을 가진다.

1. 나타내는 Value가 같다면, 인스턴스가 같지 않아도(참조주소가 같지 않아도) 두 객체는 같다.
2. Value Object는 Immutable해야 한다. 
   - 즉, 한번 객체가 생성되면 그 객체가 나타내는 Value는 GC에 의해 소멸되기 전까지 항상 동일해야 한다.

<br>

이러한 Value Object를 설계하기 위해서 다양한 방법들이 존재한다.

1. 나타내는 Value가 같다면, 인스턴스가 같지 않아도 두 객체가 같음

   - 간단하게 public boolean isSame(ValueObject other) 와 같은 비교함수를 만들어 값을 비교할 수 있다.

     ("나중에 필요할 것 같다는 생각으로" hashCode와 Equals를 Override하지 말자, 필요한 상황이 생기면 그때 Override하면 된다.)

2. Value Object를 Immutable하게 만들기:

   - 모든 속성은 private이며 setter는 없고 getter만 존재해야 한다.
     - 단, 모든 속성에 대하여 getter를 만들 필요는 없다. getter가 필요한 경우에만 추가하자.
   - getter만 존재하면 클래스 외부에서 값을 변경할 수 없지만, 클래스 내부에서 다른 클래스의 value를 변경할 수도 있기 때문에 final로 값을 선언해주는 것도 좋다.

3. 추가, Self Validation:

   - 해당 Value Object를 생성할 때 유효한 값인지를 검증해주면 좋다.

   - 예를 들어, Age라는 VO를 만들었는데 Age가 -1살일 수는 없다.

   - 생성자에서 Value가 유효한 값인지 확인하고, 유효한 값이 아니면 Execption을 Throw 해준다.

     ```java
     final class Rank {
         private int value;
     
         public Rank(int value) {
             if (value < 1 || value > 13) {
                 throw new InvalidRankValue(value);
             }
     
             this.value = value;
         }
     }
     ```

     