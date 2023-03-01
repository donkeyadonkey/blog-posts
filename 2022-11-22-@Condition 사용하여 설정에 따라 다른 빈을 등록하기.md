---
aliases: []
categories: [조각글, ]
date-created: November 22, 2022 1:38 PM
tags:
  - Spring
  - 개발 일반
date-updated: 2023-02-27 06:07
---
<br><br>
## `@Condition` 어노테이션을 사용하여 빈 등록 조건 주기

> 빈을 등록하는 조건을 주어서 어떤 구현체를 빈으로 등록할 지 결정하도록 만들 수 있다.

### A. `Condition` 인터페이스를 구현하는 조건 만들기

> 설정을 환경변수에서 가져오는 방법으로 정의한다.

1. 카프카를 사용한다는 조건

```java
public class OnKafkaEventProcessingStrategy implements Condition {
    
    public static final String KAFKA = "kafka";
    
    @Override
    public boolean matches(ConditionContext context, @NonNull AnnotatedTypeMetadata metadata) {
        return Objects.equals( context.getEnvironment().getProperty( Recody.RECODY_EVENT_PROCESSOR_TYPE ), KAFKA );
    }
}
```

1. 스프링 이벤트를 사용한다는 조건

```java
public class OnSpringEventProcessingStrategy implements Condition {
    
    public static final String SPRING = "spring";
    
    @Override
    public boolean matches(ConditionContext context, @NonNull AnnotatedTypeMetadata metadata) {
        return Objects.equals( context.getEnvironment().getProperty( Recody.RECODY_EVENT_PROCESSOR_TYPE ), SPRING );
    }
}
```

- application.yml

```java
recody:
  event:
    processor:
      type: spring
```

- 프로퍼티 이름 변수

```java
...
		public static final String RECODY_EVENT_PROCESSOR_TYPE = "recody.event.processor.type";
...
```

### B. 빈 정의하기

> `@Conditional` 어노테이션의 `value` 프로퍼티에 각각 필요한 `Condition` 구현체를 명시한다.

- 인터페이스

```java
public interface CatalogUserEventHandler {
    
    void on(UserCreated event);
    
}
```

1. 카프카 구현체

```java
@Component
@Conditional(value = OnKafkaEventProcessingStrategy.class )
@RequiredArgsConstructor
@Slf4j
@KafkaListener( topics = RecodyTopics.USER, groupId = "catalog" )
class KafkaCatalogUserEventHandler implements CatalogUserEventHandler {
    
    private final CatalogUserRepository catalogUserRepository;
    
    @Override
    @Transactional
    @KafkaHandler
    public void on(@Payload UserCreated event) {
				...
		}
}
```

1. 스프링 구현체

```java
@Component
@Conditional(value = OnSpringEventProcessingStrategy.class)
@RequiredArgsConstructor
@Slf4j
class SpringCatalogUserEventHandler implements CatalogUserEventHandler{

    private final CatalogUserRepository catalogUserRepository;

    @Override
    @EventListener
    public void on(UserCreated event) {
				...
		}
}
```