---
aliases: []
categories: []
date-created: October 12, 2022 12:19 PM
Project: Recody
tags: [JPA, 개발 일반]
date-updated: 2023-02-27 06:03
---

[[JPA] 일반 Join과 Fetch Join의 차이](https://cobbybb.tistory.com/18)

[Fetch Join vs 일반 Join(feat.DTO)](https://velog.io/@heoseungyeon/Fetch-Join-vs-%EC%9D%BC%EB%B0%98-Joinfeat.DTO)

join : 연관관계에 있는 lazy 엔티티는 바로 쿼리되지 않는다. 

fetch join : 조인되는 연관관계 엔티티까지 모두 쿼리한다. 
<br><br>
## n+1 문제

> 모든 게시물 중에서 작성자의 이름을 출력
> 
- Post 테이블에서 게시물을 모두 가져와 user id 를 알아낸다.
- User 테이블로가서 user id 에 맞는 username 을 가져온다.

- 이때 post 의 개수가 5 개라면,
    - post 5 개 쿼리 한번에 쿼리 (1)
    - post 마다 username 을 알기위해 User 테이블 각각 쿼리 (5 개)

### fetch join

```bash
@Query("SELECT p FROM Post p "
      + "JOIN FETCH p.user")
List<Post> findAllByFetchJoin();
```

- Post 와 User 를 조인하여 Post 5 개를 쿼리할 때 User 테이블에서 username 도 함께 가져옴 (1 번의 쿼리)
- 따라서 username 정보도 모두 존재한다.

### 일반 조인

```bash
@Query("SELECT p FROM Post p "
      + "LEFT JOIN User u "
      + "ON p.user.id = u.id")
List<Post> findAllByJoin();
```

- Post 를 쿼리하면서 User 테이블을 조인한다.
- 이때 User 테이블에서 가져오는 정보는 user id 외에는 없다.
- 따라서 username 을 알기위해 각 post 에 있는 user id 로 username 을 알기위해 다시 쿼리를 날려야 한다.

### Select 절에 u.username 도 함께 가져오면 되는 것 아님?

- 위와 같이 직접 쿼리를 적을 때에는 가능하지만 보통 jpa 쿼리 메서드를 사용하게 되면 select 절을 통제하는 것이 쉽지 않다. 쿼리테이블 외에 조인하는 테이블의 내용을 select 절에 쓰려면 결국은 위와 같이 쿼리를 직접 써야한다.
- 이 과정을 대신해주는 것이 Fetch Join 이다.
- Fetch Join 은 다른 것이 아니라 조인하는 테이블의 정보도 select 절에서 가져와 주는 기능이다.

Fetch Join 은 조인하는 테이블의 정보를 함께 가져오는 것이 일반 Join 보다 효율적이고 속도가 빠릅니다. 또한 연관관계 엔티티를 바로 쿼리할 수 있어 n+1 문제를 해결할 수 있습니다. 다만 Fetch Join 은 엔티티를 쿼리하는 것이기 때문에, 엔티티가 많을 때 메모리 부담이 될 수 있습니다. 따라서 쿼리를 날리는 상황과 엔티티의 상황을 고려해 각각의 조건에 맞는 Join 을 사용하는 것이 좋습니다.
