---
aliases: []
categories: []
date-created: April 11, 2022 1:32 PM
tags:
  - Web
date-updated: 2023-02-27 05:57
---
<br><br>
## Spring Security 의 필터

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/0f5aefa969e3544bc5c8b468c1b5a279.png)

> 기본적으로 서블릿 필터들에 많은 기반을 두고 있다.
>
> - 요청이 오면 여러 Filter 와 Servlet 을 가지는 **FilterChain** 을 만든다.
> - 하나의 서블릿은 하나의 요청을 처리할 수 있지만 필터는 여러개 통할 수 있다.
> - 필터는 FilterChain 을 주입받는다.
> 	- doFilter 메서드 쓰면 다음 필터로 넘어가는 데 그때 쓰는 객체가 FilterChain 객체였음.
> - 필터의 순서는 매우 중요

### **DelegatingFilterProxy**

> 필터의 구현체 중 하나.
>
> - 서블릿 컨테이너 라이프사이클과 ApplicationContext 를 이어준다.
> - 스프링이 만들어주는 **DelegatingFilterProxy** 라는 빈은 서블릿 컨테이너가 뭔지 모른다.
> 	- 서블릿 컨테이너는 스프링 컨테이너에 있는 빈을 모름.
> - 이 프록시 빈이 등록되면 스프링 컨테이너에 있는 Filter 의 구현체에게 모두 일을 맡김.
> - → 서블릿 레벨이 아니라 스프링 레벨의 필터를 사용할 수 있게 해준다.

### **FilterChainProxy**

> Spring Security 가 제공하는 필터의 한 종류로, `SecurityFilterChain` 을 통해 다른 필터에게 일을 위임한다.
>
> - 물론 서블릿이 작동하는 맥락에서 사용될 수 있도록 `DelegatingFilterProxy` 로 감싸져서 작동하게 된다.

### SecurityFilterChain

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/9a755c2e7e2c58640bef281c463b1f4e.png)

> 어떤 Spring Security Filter(즉, 어떤 SecurityFilterChain) 가 사용되어야 하는지 결정한다.
>
> - Spring Security 필터들은 보통 스프링이 제공하는 `FilterChainProxy` 가 등록해준다.
> - Spring 레벨의 DelegatingFilterProxy 가 직접 결정하지 않고 `FilterChainProxy` 를 씀으로써 다양한 이점이 있다.
>
> 	- Spring Security 가 서블릿 관련 기능을 직접 지원한다.
> 	- 필터는 url 에 의존하여 호출되지만 `FilterChainProxy` 는 요청 객체의 어느 부분에 대해서든 호출될 수 있다.
> 		- 이때 RequestMatcher 를 사용함.
>
> 	  <aside>
> 	  📌 결과적으로 애플리케이션 설계와 분리되어서 보안 관련 로직을 분리되고 훨씬 구체적이게 만들 수 있게 되는 듯.
>
> 	  </aside>

> 어떤 SecurityFilterChain 을 사용할 것인가?
>
> - 더 구체적인 URL 패턴에 해당하는 필터 체인
> 	- 예를 들어 /** 보다는 /api/** 에 해당되는 요청이 있으면 그 필터 체인이 적용됨.

> 필터의 개수도 자유롭다.
>
> 필터체인에 있는 필터는 독립적이고 어느 필터체인에 넣을 수 있다. 0 개도 넣을 수 있음.
<br><br>
## Security Filters

<aside>
📌 필터 종류를 다 알필요는 없지만 필요할 때 찾아보면 될 것 같다. 
문서에서도 모든 필터에 대해 설명하지는 않고, 동작 예시를 보고 직접 코드 내 구조를 파악해서 사용하자.

</aside>

[Architecture](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-security-filters)
