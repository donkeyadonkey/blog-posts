---
aliases: []
categories: [조각글, ]
date-created: October 11, 2022 2:44 PM
Project: Recody
tags: [QueryDsl, 개발 일반]
date-updated: 2023-02-27 06:02
---

<br><br>
# Sort 활용하기

Sort.by(”id”) 와 같이 column 의 이름으로 정렬한다. 

이때 어떤 테이블인지 (entity 인지) 명시하지 않는데, 만약 해당 entity 에 존재하지 않는 컬럼이면 예외를 던진다.

- 실행 코드

```bash
// given
PageRequest pageable = PageRequest.of(1, 100, Sort.by("sample"));

// when
recordRepository.findAllByContentIdAndUserId(USER_ID, CONTENT_ID, pageable);
```

- 예외

```bash
org.hibernate.QueryException: could not resolve property: sample of: com.recody.recodybackend.record.data.record.RecordEntity [select recordEntity
from com.recody.recodybackend.record.data.record.RecordEntity recordEntity
  left join recordEntity.content
where recordEntity.userId = ?1 and recordEntity.content.contentId = ?2
order by recordEntity.sample asc];
...
```

```bash
Caused by: java.lang.IllegalArgumentException: org.hibernate.QueryException: could not resolve property: sample of: com.recody.recodybackend.record.data.record.RecordEntity [select recordEntity
from com.recody.recodybackend.record.data.record.RecordEntity recordEntity
  left join recordEntity.content
where recordEntity.userId = ?1 and recordEntity.content.contentId = ?2
order by recordEntity.sample asc]
```

querydsl 이 사용하는 `OrderSpecifier` 객체로 바꿀때에는 해당 컬럼이 실제 존재하는지 체크하지 않는다. 주어진 요소들로 쿼리를 만들어 실행하지만 위와 같이 어떤 컬럼인지 찾지 못해 발생하는 예외이다. 