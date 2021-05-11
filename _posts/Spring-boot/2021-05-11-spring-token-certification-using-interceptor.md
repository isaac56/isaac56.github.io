---
layout: post
title:  "spring interceptor로 토큰 기반 인증 구현"
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
## [이번 프로젝트](https://github.com/isaac56/baseball/)에서는 자체 jwt를 발급하여 토큰 기반 인증을 적용했다.

이번엔 아래와 같이 oauth 로그인 후엔 토큰 기반 인증을 하도록 설계하였다.

![](https://user-images.githubusercontent.com/56751259/117400886-7b83cb00-af3e-11eb-8ac0-6656c87c72b4.png)

그리고 한번 로그인 후에 거의 대부분의 api에 대해서 token이 유효한지 (인증) 등을 확인해야 한다.

모든 controller method에 token을 검사하는 코드가 중복되어 들어간다면 번거로울 뿐만 아니라 유지/보수에도 좋지 않다.

그래서 이번엔 interceptor를 활용하여 인증이 필요한 api는 interceptor의 preHandle 에서 인증 검사를 하도록 만들어 보았다. (Spring Security는 어려울 것 같아서 나중에 적용해야겠다.)



## interceptor 생성

```
@Component
public class CertificationInterceptor implements HandlerInterceptor {
    private final UserService userService;

    @Autowired
    public CertificationInterceptor(UserService userService) {
        this.userService = userService;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        String jwt = request.getHeader("Authorization");
        if (jwt == null) {
            throw new UnauthorizedException("Authorization 코드가 존재하지 않습니다.");
        }

        request.setAttribute("userId", userService.getUserIdFromJwt(jwt));
        return true;
    }

    private void setJsonResponse(Object object, HttpServletResponse response) throws IOException {
        ObjectMapper mapper = new ObjectMapper();
        mapper.getFactory().configure(JsonWriteFeature.ESCAPE_NON_ASCII.mappedFeature(), true);
        response.setContentType("application/json");
        response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
        response.getWriter().write(mapper.writeValueAsString(object));
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
                           ModelAndView modelAndView) throws Exception {
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
            throws Exception {
    }
}
```

HandlerInterceptor를 상속받아서 쉽게 구현이 가능하다.

코드를 읽어보면, preHandler에서 request header의 Authorization값(jwt)를 읽어서 UserService 내부의 메소드를 사용해 userId를 넘겨준다.

 (UserService.getUserIdFromJwt 메소드 내부에서 jwt 유효성 검사를 진행하며 잘못되었으면 Exception을 던진다.)



이렇게 정의해놓으면, jwt가 유효하지 않을 때 throw 된 Exception을 GlobalExceptionHandler가 받아서 적절한 에러메세지를 반환해주고, 제대로 확인이 되었으면 controller의 메소드가 실행된다.



## Interceptor 등록

```
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
    private HandlerInterceptor interceptor;

    @Autowired
    public WebMvcConfig(@Qualifier(value = "certificationInterceptor") HandlerInterceptor interceptor) {
        this.interceptor = interceptor;
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(interceptor)
                .addPathPatterns("/**")
                .excludePathPatterns("/user/**/")
                .excludePathPatterns("/game/list");
    }
}

```

Configuration을 통해 사용할 interceptor와 interceptor를 적용할 url를 지정해준다.

addPatchPatterns으로 적용할 url을 지정할 수 있고, excludePathPatterns로 적용하지 않을 url을 지정할 수 있다.