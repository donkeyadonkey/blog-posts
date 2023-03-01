---
aliases: []
categories: [조각글]
created-time: October 23, 2022 5:59 PM
Project: Recody
tags: [Spring, 개발 일반]
date-updated: 2023-02-27 06:04
---

Async 로 새로운 쓰레드에서 작업이 시작될때 트랜잭션을 만들어 줘야한다. 

구현체 메서드에서 `@Transactional` 을 붙여도 정상적으로 트랜잭션이 만들어지지 않았다.

(내부 로직에서 findById 때문에 readOnly 트랜잭션이 생셩되긴 했지만 저장 로직이 필요해 정상적으로 이루어지지 않았다.)

```java
@Async
@Transactional
default CompletableFuture<List<T>> registerAsync(List<SOURCE> source, Locale locale){
    return CompletableFuture.completedFuture(this.register(source, locale));
}
```

- readOnly transaction 로그

```java
2022-10-23 17:56:37.706 DEBUG 76073 --- [   movie-task-2] o.s.orm.jpa.JpaTransactionManager        : Creating new transaction with name [org.springframework.data.jpa.repository.support.SimpleJpaRepository.findById]: PROPAGATION_REQUIRED,ISOLATION_DEFAULT,readOnly
```

- 정상적 트랜잭션 로그

```java
2022-10-23 17:56:37.688 DEBUG 76073 --- [   movie-task-1] o.s.orm.jpa.JpaTransactionManager        : Creating new transaction with name [com.recody.recodybackend.movie.features.manager.DefaultCountryManager.registerAsync]: PROPAGATION_REQUIRED,ISOLATION_DEFAULT
```