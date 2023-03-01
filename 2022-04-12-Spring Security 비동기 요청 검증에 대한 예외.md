---
aliases: []
categories: []
date-created: April 12, 2022 5:58 PM
tags: [Web]
date-updated: 2023-02-27 05:57
---
<br><br>
# 기본적으로 예외처리를 하는 구조에 관해서

[Architecture](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-exceptiontranslationfilter)

> 예외 처리는 `ExceptionTranslationFilter` 가 수행한다. 다른 필터들을 모두 통과하게 하고, 예외가  발생하면 그때 동작한다.
> 
- 현재 `SecurityContextHolder` 가 비워진다.
- 요청 객체가 `RequestCache` 에 저장된다.
- 사용자가 인증 또는 인가를 다시 시도하고, 안되면 예외를 발생시킨다. `AccessDeniedException` 또는 `AuthenticationException`
- 아무 예외도 터지지 않으면 `ExceptionTranslationFilter` 는 아무것도 하지 않는다.
<br><br>
## 기본전략

### LoginUrlAuthenticationEntryPoint

> `UsernamePasswordAuthenticationFilter` 에서 예외가 나면 `ExceptionTranslationFilter` 가 `LoginUrlAuthenticationEntryPoint` 를 사용하여 로그인 페이지로 리다이렉트 하는것으로 보인다.
> 
<br><br>
## 고려해볼 인터페이스

### AuthenticationEntryPoint

### AuthenticationFailureHandler

### AuthenticationSuccessHandler
<br><br>
## 사용할 수 있는 구현체

### LoginUrlAuthenticationEntryPoint
<br><br>
# 내가 사용한 방법

1. 비동기 요청임을 알려주는 커스텀 헤더를 프론트엔드에서 보낸다.
2. 헤더를 보고 리다이렉트하지 않도록 백엔드에서 처리해준다.

> 기본적으로 사용되는 `LoginUrlAuthenticationEntryPoint` 를 상속받아서 비동기 요청에 대해서만 에러를 응답하도록 바꿔주었다.
> 
> - 그 외에는 기존 방식을 사용하도록 하였다.
> - 단, 프론트엔드 에서의 비동기 요청인지 알기위해 커스텀 헤더를 달아 보내도록 하였다.
>     - 그 헤더를 보고 에러를 응답하면 MVC 에 의해 401 응답을 보내게 된다. (302 대신에)

> 이렇게 처리하는 예시도 참고하긴 했지만 굳이 존재하는 구현체를 상속해서 했어야 했나 싶다.
> 
> - 프론트 엔드에서 헤더도 달아줘야 하는 과정을 거쳐야하는게 번거롭기도 하고.
> - 다른 방법이 있을 것 같긴 하다.

```java
@Slf4j
public class AjaxAuthenticationEntryPoint extends LoginUrlAuthenticationEntryPoint {
    
    public AjaxAuthenticationEntryPoint(String loginFormUrl) {
        super(loginFormUrl);
    }
    
    @Override
    public void commence(HttpServletRequest request,
                         HttpServletResponse response,
                         AuthenticationException authException) throws IOException, ServletException {
        
        Boolean isAjax = Boolean.parseBoolean(request.getHeader("AJAX"));
        log.debug("isAjax={}", isAjax);
        
        if (isAjax) {
            response.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Unauthorized");
        } else {
            super.commence(request,response,authException);
        }
    }
}
```

> 그리고 나서 설정에 등록해준다
> 

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
		...
		// 인증에 실패할 경우 보낼 곳
    http.exceptionHandling()
        .authenticationEntryPoint(new AjaxAuthenticationEntryPoint("/auth/login"));
		...
}
```

[REST API 구성시, Spring Security 구현](https://netframework.tistory.com/entry/REST-API-%EA%B5%AC%EC%84%B1%EC%8B%9C-Spring-Security-%EA%B5%AC%ED%98%84)

[Spring Security에서 Ajax 권한 검사하는 법](https://rlog.or.kr/post/94)

[Spring Security + Rest](https://taeu.kr/14)

[[Spring Boot] 로그인 실패 핸들링 - Spring Security](https://hooongs.tistory.com/235)
