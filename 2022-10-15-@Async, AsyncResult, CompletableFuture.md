---
aliases: []
categories: [조각글]
Created Time: October 15, 2022 8:36 PM
Project: Recody
tags: [Spring, 개발 일반]
date-updated: 2023-02-27 06:03
---
<br><br>
# @Async, AsyncResult, CompletableFuture
<br><br>
## @Async 를 사용할 때 반환 타입

기본적으로 void, Future 를 지원한다.

이때 Future 타입에 스프링에서 제공하는 `AsyncResult<V>` 를 사용할 수 있다. 

- CompletableFuture.completedFutre(…)

- @Async 가 붙은 메서드는 같은 클래스 내에서는 사용할 수 없다.

[@Async not working for method having return type void](https://stackoverflow.com/questions/56382562/async-not-working-for-method-having-return-type-void)

### 기본 Executor 빈

**SimpleAsyncTaskExecutor**

- 쓰레드를 재사용하지 않는다고함…

[SimpleAsyncTaskExecutor (Spring Framework 5.3.23 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/task/SimpleAsyncTaskExecutor.html)

```java
timeout 5 bash -c "</dev/tcp/ecs-lb-kafka-6cc589fd2de00ff9.elb.ap-northeast-2.amazonaws.com/9093"
```