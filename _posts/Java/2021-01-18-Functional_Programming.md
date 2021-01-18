---
layout: post
title:  "함수형 프로그래밍"
excerpt: ""

categories:
  - Java
tags:
  - [Java, Functinal Programming]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-18
last_modified_at: 2021-01-18
---
## 함수형 프로그래밍이란?

> 객체지향은 동작하는 부분을 캡슐화해서 이해할 수 있게 하고, 함수형 프로그래밍은 동작하는 부분을 최소화해서 코드 이해를 돕는다.
>
> - **Michael Feathers** ‘레거시 코드 활용 전략' 저자

객체지향 프로그래밍에서는 각 객체안에 자신이 기능하기 위한 상태(변수)와 메소드 들이 캡슐화 되어있고,

그러한 객체 간의 상호작용을 통해 프로그램이 동작하게 된다.

<br>

여기서 객체의 메소드는 객체안의 상태(멤버 변수)에 따라 같은 파라미터를 입력하더라도 결과값이 다를 수 있고,

비동기 프로그래밍을 위해서는 특정 변수에 Lock을 걸어 스레드간의 충돌이 일어나지 않도록 신경써야 한다.

즉, 객체의 상태에 따른 side effect를 주의하여야 하며, 여러 스레드에서 같은 상태(멤버 변수)에 접근하여 side effect가 발생하지 않도록 주의해야 한다.

<br>

이렇게 side effect를 걱정하며 신경 쓸 거리를 줄이고, 코드를 간결하게 하기 위해 나온 패러다임이 함수형 프로그래밍이라고 할 수 있다.

<br>

## 함수형 프로그래밍의 컨셉

1. 변경 가능한 상태를 불변상태(Immutable)로 만들어 SideEffect를 없애자.
2. 모든 것은 객체이다.
3. 코드를 간결하게 하고 가독성을 높여 구현할 로직에 집중 시키자.
4. 동시성 작업을 보다 쉽게 안전하게 구현 하자.

### 변경 가능한 상태를 불변상태(Immutable)로 만들어 SideEffect를 없애자.

함수형 프로그래밍에서 함수는 자체적인 상태를 관리하지 않는다.

인자로 들어온 변수와 상수만을 이용하여 결과를 출력함으로써, 동일한 인풋일 때 항상 같은 결과임을 보장한다.

코드로 표현하면 아래와 같다.

```java
// 함수형 프로그래밍에서는 아래와 같이
// 동일한 인자를 입력받으면 항상 동일한 결과를 반환하여야 한다.
// x=1, y=1일 경우 func는 항상 2르르 반환한다.
public class Test {
    public int func(int x, int y) {
        return x + y;
    }
}


// 객체지향 프로그래밍에서는 아래와 같이
// 동일한 인자를 입력받더라도 객체의 상태에 따라 다른 결과를 반환할 수 있다.
// 예제에서는 state가 변경될 때 발생할 수 있는 side effect를 주의하며 코딩해야 한다.
public class Test {
    int state = 0;

    public int func(int x, int y) {
        if(state == 0)
            return x + y;
        else
            return x * y;
    }
}
```

<br>

### 모든 것은 객체이다.

함수형 프로그래밍에서는 함수 또한 1급 객체로 사용할 수 있어야 한다. 

1급 객체가 되려면 값으로 할당 할 수 있고, 파라미터로 다른 함수에 넘길 수 있고, 리턴값으로 반환할 수 있어야 한다.

<br>

안타깝게도, 자바에서는 함수를 1급 객체로 취급하지 않기 때문에 함수(메소드)를 담은 인스턴스를 넘기는 방식으로 

메소드를 인자로 전달하거나 결과로 반환받을 수 있다.

<br>

다만 자바 8부터는 람다식을 통해, 마치 메소드를 인자로 넘기는 것처럼 인스턴스를 넘길 수 있게 되었다.

- 자바8부터 메소드가 하나뿐인 인터페이스는, 람다식으로 익명 클래스를 간단하게 선언할 수 있다.
- 실제 동작은 람다식으로 익명 클래스를 생성하고, 그 익명 클래스를 넘겨주는 것이다.

<br>

예제는 다음과 같다.

```java
// 메소드가 하나뿐인 인터페이스만
// 람다식을 통해 익명 클래스로 선언될 수 있다.
public interface Calculator{
	public int calculate(int a, int b);
}

public class CalcService{
  public int service(Calculator c, int a, int b){
    return c.caculate(a,b);
  }
}

public class Main{
  public static void main(String[] args) {
    CalcService calc = new CalcService;
    int result = calc.service((int a, int b) -> a*b, 3, 5);
    System.out.println(result); //15출력
  }
}
```

<br>

### 코드를 간결하게 하고 가독성을 높여 구현할 로직에 집중 시키자.

아예 변경하지 않고 재사용하거나, 아주 일부만을 변경하여 재사용하는 코드들을 보일러 플레이트 코드라고 한다.

<br>

예를 들어, List에 있는 모든 변수들에 어떤 처리를 해주기 위해서는 List를 포문으로 순회하며 처리해야 한다.

이때 "처리"에 해당하는 부분만 변경될 뿐 포문은 항상 재사용되기 때문에 포문을 보일러 플레이트 코드라고 할 수 있다.

<br>

함수형 프로그래밍에서는 "처리"에 해당하는 함수를 인자로 받는 고계함수를 만들어서 보일러 플레이트 코드를 없앨 수 있다.

(고계함수는 함수를 처리하는 함수라는 뜻으로, 함수를 인자로 받아 사용하거나 함수를 반환하는 등의 행위를 할 수 있다.)

<br>

예를 들어, Integer의 List에 있는 모든 요소를 제곱으로 변경해보겠다.

```java
import java.util.List;

public interface HandleType<T> {
    public T func(T x);
}

public class ForStatement<T> {
    public void change(List<T> list, HandleType<T> handle){
        for(int i = 0; i < list.size(); i++) {
            list.set(i, handle.func((list.get(i))));
        }
    }
}

public class Main {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
        list.add(4);
        list.add(5);
        list.stream().forEach();

        ForStatement<Integer> forStatement = new ForStatement<>();
        forStatement.change(list,(Integer i) -> i*i); // 모든 요소를 제곱으로 변경
    }
}
```

이처럼 ForStatement와 같은 클래스를 한번 정의해두면,

 앞으로 List 전체 요소를 변경할 일이 있을 때 for문을 작성할 필요 없이 간단하게 원하는 값으로 변경할 수 있다.

<br>

즉, 함수형 프로그래밍에서는 핵심 로직(함수)만 인자로 넘겨줌으로써 코드를 간결하게 하고 로직에 집중할 수 있게 해준다.

그리고 자바8부터는 stream을 제공하여 위와 같은 과정을 쉽게 구현할 수 있다.

- [자바8 stream 사용법](https://futurecreator.github.io/2018/08/26/java-8-streams/)

<br>

### 동시성 작업을 보다 쉽게 구현 하자

함수형 프로그래밍에서 함수는 별도의 상태에 영향을 받지 않는다.

파라미터로 받은 인풋에 의해 값이 결정되기 때문에 동시에 여러 스레드에서 접근하더라도 Side Effect가 발생하지 않는다.

별도의 Lock, Unlock을 할 필요도 없어, 동시성 작업이 보다 간결해지는 것이다.

