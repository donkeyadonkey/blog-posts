---
aliases: []
categories: [조각글]
date-created: October 8, 2022 12:22 PM
Project: Recody
tags: [QueryDsl, 개발 일반]
date-updated: 2023-02-27 06:01
---

```java
jpaQueryFactory
     .selectFrom(recordEntity)
     .where(recordEntity.content.category.eq(category), recordEntity.userId.eq(userId))
     .orderBy(recordEntity.createdAt.desc())
     .fetch();
```

이렇게 하면 크로스조인을 한다. 

### 명시적으로 조인한다.

> 조인할 테이블은 왼쪽 테이블을 기준으로 탐색하는 방식으로 가야한다.
> 

```java
jpaQueryFactory
           .selectFrom(recordEntity)
           .leftJoin(recordEntity.content)
           .where(recordEntity.content.category.eq(category), recordEntity.userId.eq(userId))
           .limit(pageable.getPageSize())
           .offset(pageable.getOffset())
           .orderBy(recordEntity.createdAt.desc())
           .fetch();
```

```java
Hibernate: 
    select
        recordenti0_.record_id as record_i1_11_,
        recordenti0_.created_at as created_2_11_,
        recordenti0_.last_modified_at as last_mod3_11_,
        recordenti0_.appreciation_date as apprecia4_11_,
        recordenti0_.completed as complete5_11_,
        recordenti0_.content_id as content10_11_,
        recordenti0_.note as note6_11_,
        recordenti0_.nth as nth7_11_,
        recordenti0_.title as title8_11_,
        recordenti0_.user_id as user_id9_11_ 
    from
        record recordenti0_ 
    left outer join
        record_content recordcont1_ 
            on recordenti0_.content_id=recordcont1_.id 
    where
        (
            recordcont1_.category_id, recordcont1_.name
        )=(
            ?, ?
        ) 
        and recordenti0_.user_id=? 
    order by
        recordenti0_.created_at desc limit ?
```