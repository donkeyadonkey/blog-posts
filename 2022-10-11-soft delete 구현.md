---
aliases: []
categories: []
date-created: October 11, 2022 5:18 PM
Project: Recody
tags: [JPA, 개발 일반]
date-updated: 2023-02-27 06:03
---

[[JPA] JPA + Hibernate 기반의 개발 환경에서 Soft Delete 구현하기](https://velog.io/@nmrhtn7898/JPA-JPA-Hibernate-%EA%B8%B0%EB%B0%98%EC%9D%98-%EA%B0%9C%EB%B0%9C-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C-Soft-Delete-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0)
<br><br>
## Soft Delete?

Soft Delete 는 데이터를 관리하기 위한 일종의 기능으로, 데이터를 삭제할 때 명시적으로 삭제되지 않고 삭제된 것으로 간주되도록 처리하는 것을 말한다. 이러한 기능을 사용하면 데이터의 삭제를 되돌릴 수 있고, 다른 테이블에서 레퍼런스를 가지고 있는 레코드는 삭제되지 않고 계속 보관되기 때문에 데이터의 손실을 줄일 수 있다.

사용자는 대부분 삭제되었다고 간주되고, 실제로는 데이터가 보관되고 있다고 명시해야한다. 또한, 삭제된 레코드는 삭제 테이블을 사용하여 보관하고, 다른 테이블에서 레퍼런스를 가지고 있는 경우 연관관계가 있는 레코드도 함께 삭제되도록 처리해야 한다.
<br><br>
# 개요

이 문서는 JPA 를 사용하여 soft delete 구현 방법을 소개합니다. 방법 1 에서는 `is_deleted` 칼럼이나 `delete_at` 으로 시간을 명시하는 방법, 방법 2 에서는 `deleted_{테이블이름}` 을 사용하는 방법을 소개합니다. 또한, 다른 테이블에서 레퍼런스를 가지는 경우 어떻게 처리할지, `delete()` 메서드나 repository 를 사용할 때 어떻게 구현할 수 있는지 각각 설명합니다.
<br><br>
## 방법 1

> is_deleted 칼럼 (not null) 사용하기
> 
> - 또는 delete_at(nullable) 으로 시간을 명시하기.

### 삭제할 레코드가 다른 테이블에서 레퍼런스를 가지는 경우에는?

- 다른 테이블을 쿼리할 때 그 삭제된 레코드를 참조하는지 체크하고 쿼리해야한다. (모든 쿼리가 복잡해진다.)
- cascade 삭제 처리 (자식 레코드들을 모두 deleted = true 처리) 하는 경우의 문제도 있다.
    - jpa 에서 프록시를 가져오는 경우, delete=true 도 가져온다. 따라서 삭제되지 않은 것으로 간주되게된다.
    - 자식 테이블과 연관관계가 존재하는 레코드가 lazy loading 되는  경우에도 삭제된 레코드가 포함될 수 있다.
        - 이때 실제 그 레코드를 쿼리하면 delete=true 가 되어있기 때문에 찾을 수 없다.
        - 이런 경우는 부모 레코드를 삭제하는 경우, 자식도 함께 삭제처리되게 해야한다. 하지만 역시 또다른 테이블에서 프록시 객체가 사용되는 경우, 쿼리될 수 있다.
<br><br>
## 방법 2

> 삭제된 레코드들을 보관하는 deleted_{테이블이름} 사용하기
> 
> - 실제로 테이블에 존재하던 레코드는 삭제한다.
> - select 쿼리할 때 따로 처리해줄 필요도 없다.
>     - 따라서 삭제되지 않은 테이블에 복잡한 where 문을 쓰거나 하지 않기 때문에 성능적으로도 이득이 클듯.

### 삭제할 레코드를 다른 테이블에서 레퍼런스를 가지는 경우에는?

그 레코드와 관련된 모든 것들을 삭제해야한다. 그럴 경우, 삭제 테이블을 여러개 만들어야하는 상황이 생긴다.
<br><br>
## 적용

### delete() 메서드 사용시

```bash
update
        record 
    set
        created_at=?,
        last_modified_at=?,
        appreciation_date=?,
        completed=?,
        deleted_at=?,
        note=?,
        nth=?,
        title=? 
    where
        record_id=?
```

### repository 사용시

> `@SQLDelete(sql = "UPDATE record SET deleted_at = NOW() WHERE record_id=?")`
> 

```bash
Hibernate: 
    UPDATE
        record 
    SET
        deleted_at = NOW() 
    WHERE
        record_id=?
```
<br><br>
## 주의

- 레퍼런스는 여전히 쿼리된다. 따라서 lazy loading 은 주의

### 참조하는 테이블의 경우 자식 엔티티도 똑같은 soft delete 구문이 있어야 삭제처리된다.

```bash
@Entity
@SuperBuilder
@AllArgsConstructor
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter
@Table(name = "record")
@Where(clause = "deleted_at IS null")
@SQLDelete(sql = "UPDATE record SET deleted_at = NOW() WHERE record_id=?") // repository 사용하는 경우.
public class RecordEntity extends RecordBaseEntity {

		...

		@OneToOne(cascade = CascadeType.REMOVE, orphanRemoval = true)
		private TestEntity testEntity;
...
}
```

```bash
@Entity
@Where(clause = "deleted_at IS null")
@SQLDelete(sql = "UPDATE test_entity SET deleted_at = NOW() WHERE id=?")

public class TestEntity {
    
    @Id
    private Long id;
    
    @Column(name = "deleted_at")
    private LocalDateTime deletedAt;
...
```

```bash
UPDATE
        test_entity 
    SET
        deleted_at = NOW() 
    WHERE
        id=?
```