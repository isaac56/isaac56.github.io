---
layout: post
title:  "spring interceptor와 exception catch"
excerpt: ""

categories:
  - Spring-boot
tags:
  - [Spring-boot]

toc: true
toc_sticky: true
comments: true
 
date: 2021-05-11
last_modified_at: 2021-05-11
---
## Spring Interceptor란?

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile10.uf.tistory.com%2Fimage%2F992590395ABF406F180F86)

위 그림에서 HandlerInterceptor 부분이 보일 것이다.

<br>

간단하게 얘기하자면, Handler가 적절한 controller의 메소드를 실행시키기 직전에 request와 response를 가로채서(intercept) 무언가 실행시키는 녀석이다.

<Br>

![](https://media.vlpt.us/images/y_dragonrise/post/26c193b6-9148-4f51-a3b6-63d23e2cffcc/image.png)

위 그림에서 보이는 request의 흐름은 다음과 같다. (preHandler가 true를 반환한다고 가정했을 때)

<Br>

request -> filter -> HandlerMapping (현재 url에 맞는 컨트롤러 찾기) -> **Handler interceptor의 preHandle 실행** -> Controller의 메소드 실행 -> **Handler interceptor의 postHandle 실행** -> View Resolver에서 View 반환 -> **Handler Interceptor의 afterCompletion 실행**

<br>

그리고, preHandler에서 false가 반환되었을 경우 spring은 interceptor에서 request의 처리를 모두 마쳤다고 생각해, preHandler 이후의 작업을 실행시키지 않는다.

<br>

## Interceptor에서 발생한 Exception

Interceptor는 DispatcherServlet 이후에 실행되기 때문에, Spring Context 내부에 존재하고 따라서 모든 빈에 접근이 가능하다.

<br>

이때 Controller 내부에 ExceptionHandler를 정의해 둔다고 해도, Interceptor 내부에서 발생하는 exception은 controller 전/후에서 발생하기 때문에 Controller 내부의 ExceptionHandler는 그 exception을 잡을 수 없다.

<br>

하지만 ControllerAdvice에 Interceptor에서 발생할 ExceptionHandler를 정의해 두었다면, ControllerAdvice로 Exception이 전달되면서 Exception을 처리할 수 있게 된다.

<br>

>If you use @EnableWebMvc annotation anywhere through ur application, `HandlerExceptionResolverComposite (subclass of HandlerExceptionResolver)` will be applied. Since we know that `HandlerExceptionResolver` will be invoked not only through the controller method execution cycle *but also* before/after controller (e.g. HandlerInterceptor. check [here](https://www.logicbig.com/tutorials/spring-framework/spring-web-mvc/handler-exception-resolver.html)), `HandlerExceptionResolverComposite` will be invoked. Since by default, HandlerExceptionResolverComposite will register 3 resolvers, and one of them is: `ExceptionHandlerExceptionResolver`, based on https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/mvc/method/annotation/ExceptionHandlerExceptionResolver.html#doResolveHandlerMethodException-javax.servlet.http.HttpServletRequest-javax.servlet.http.HttpServletResponse-org.springframework.web.method.HandlerMethod-java.lang.Exception-
>
>it will try to find controller level @ExceptionHandler annotation and forward the exception to that exception handler. (see "doResolveHandlerMethodException" in above link)
>
>So as long as you have @EnableWebMvc (why not?), your @ExceptionHandler should be able to catch exception thrown from spring interceptor.
>
>- <https://stackoverflow.com/questions/22062311/how-to-use-exceptionhandler-in-spring-interceptor/27823944>

