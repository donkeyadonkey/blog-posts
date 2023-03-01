---
aliases: []
categories: [조각글]
date-created: November 17, 2022 6:02 PM
Project: Recody
tags: [QueryDsl, 개발 일반]
date-updated: 2023-02-27 06:07
---

### fetchCount, fetchResults 가 Deprecated 되었다.

간단히 count 쿼리를 날리는 방법이긴 하지만 위의 방법으로도 count 를 할 수 없게 되었다.

JPARepository 의 쿼리 메서드로 Page 객체를 반환받는 방식이 가장 간단한데, 엔티티 관계가 복잡하거나 복잡한 로직을 필요로 하는 경우 한계가 있다. 따라서 QueryDsl 를 사용한다는 전제를 깔아두고 알아본다.
<br><br>
## 방법

- 원하는 전체 쿼리 결과를 all 에 담고 전체 개수를 도출한다.
- 두번째로 원하는 페이지에 해당하는 결과를 따로 담는다.
- `PageImpl` 클래스에 담아 반환한다.

- 쿼리를 총 두개 날린다. 성능에도 안좋을 것이고 count 쿼리를 따로 생성하지 않는 방법이기 때문에 (fetchCount() 또는 select 절에 Q 엔티티.count() 를 따로 만들지 않음) 좋은 방법은 아닌듯 하다.
- 또한 별개의 JPAQuery 객체가 아니기 때문에 사용하는 순서도 조심해야한다.
    - 쿼리에 limit, offset 을 적용하고나면 되돌릴 수 없음.(Total 을 가져올 수 없음)
    - 따라서 전체 쿼리를 먼저 fetch 해야한다.
    - 또는 clone() 을 한 다음에 추가적인 쿼리를 만들어 fetch 한다.
- 다만 쿼리하고자하는 내용이 같다는 것을 보장할 수 있음.
    - 같은 JPAQuery 객체를 사용하기 때문.

```java
@Override
public Page<MovieEntity> findPagedByTitleLike(String title, Locale locale, Pageable pageable) {
    JPAQuery<MovieEntity> query = this.createQueryFindByTitleLikeByLocale( title, locale );
    List<MovieEntity> all = query.fetch();
    List<MovieEntity> currentPageItems = query.limit( pageable.getPageSize() )
                                              .offset( pageable.getOffset() )
                                              .fetch();
    return new PageImpl<>(
            currentPageItems,
            pageable,
            all.size() );
}
```