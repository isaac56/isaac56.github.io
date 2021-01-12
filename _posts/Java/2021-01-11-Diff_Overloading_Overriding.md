---
layout: post
title:  "오버로딩과 오버라이딩"
excerpt: ""

categories:
  - Java
tags:
  - [Java]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-12
last_modified_at: 2021-01-12
---
## 오버로딩과 오버라이딩 차이

### 오버로딩

- 같은 클래스 내에서 메소드명은 같지만 파라미터가 다른 경우 오버로딩이라고 할 수 있다.

  ```java
  public class Test {
      public void method() {
          System.out.println("method1");
      }
      public void method(char a) {
          System.out.println("method2");
      }
      public void method(int a) {
          System.out.println("method3");
      }
  }
  
  public class Main {
      public static void main(String[] args) {
          Test test = new Test();
  
          test.method();    //method1 출력
          test.method('a'); //method2 출력
          test.method(1);   //method3 출력
      }
  }
  ```

  

  위에서 세 메소드 모두 이름이 method지만 파라미터가 달라 같은 이름으로 다양한 메소드 구현이 가능하다.

### 오버라이딩

- 부모 클래스와 상속받은 자식 클래스 내에서 이루어진다.

- 부모 클래스에서 구현된 메소드와 형태(이름, 리턴타입, 파라미터)가 같은 메소드를 자식 클래스에서 재정의할 경우 오버라이딩된다.

  ```java
  public class SuperClass {
      public void method(){
          System.out.println("this is superclass.");
      }
  }
  
  public class ChildClass extends SuperClass{
      public void method(){
          System.out.println("this is ChildClass");
      }
  }
  
  public class Main{
      public static void main(String[] args) {
          SuperClass test1 = new SuperClass();
          SuperClass test2 = new ChildClass();
          ChildClass test3 = new ChildClass();

          test1.method() //this is superclass. 출력
          test2.method() //this is ChildClass 출력
          test3.method() //this is ChildClass 출력
      }
  }
  ```

  위의 결과에서 알 수 있듯이, 오버라이딩을 하면 부모클래스의 메소드를 오버라이딩한 메소드로 대체하게 된다.
  
  <Br>
  
- 부모 클래스에서 정의된 메소드와 이름, 파라미터는 동일하지만 리턴 타입이 다를 경우에는 다음과 같은 에러가 발생함

  ```
  //부모클래스 메소드의 리턴타입은 void, 자식클래스 메소드의 리턴타입이 int일 때,
  java: method() in ChildClass cannot override method() in SuperClass
    return type int is not compatible with void
  ```

- 부모 메소드의 한정자보다 자식 클래스에서 오버라이드하려는 메소드의 한정자가 더 제한적이면 다음과 같은 에러 발생

  ```
  //부모클래스 메소드의 한정자는 public, 자식클래스 메소드의 한정자는 private일 때,
  java: method() in ChildClass cannot override method() in SuperClass
    attempting to assign weaker access privileges; was public
  ```

  - 부모 메소드의 한정자보다 자식 메소드의 한정자가 덜 제한적이면 오버라이딩 가능함

    (부모 메소드는 protected, 자식 메소드는 public)

  <br>

- 부모 메소드의 한정자가 private으로 제한된다면 오버라이딩이 불가하며 아래와 같은 결과가 나온다.

  ```java
  public class SuperClass {
      private void method(){
          System.out.println("this is superclass.");
      }
  
      public void show() {
          this.method();
      }
  }
  
  public class ChildClass extends SuperClass{
      private void method(){
          System.out.println("this is ChildClass");
      }
  }
  
  public class Main {
      public static void main(String[] args) {
          SuperClass t1 = new SuperClass();
          SuperClass t2 = new ChildClass();
  
          t1.show(); //this is superclass 출력
          t2.show(); //this is superclass 출력
      }
  }
  ```

  

### Override 어노테이션에 관하여

- 나는 @Override 어노테이션이 있어야만 오버라이드가 되는 줄 알았다.
- @Override 어노테이션은 컴파일러에게 "지금부터 이 메소드를 오버라이딩 할거야" 라고 알려주는 용도이다.
- 이때, 컴파일러는 부모 클래스에 동일한 메소드가 없을 경우 에러를 발생시키기 때문에, 안전한 코딩을 위해 @Override를 사용하는 것이 좋다.
- @Override 어노테이션이 없어도 오버라이딩은 잘 된다.

