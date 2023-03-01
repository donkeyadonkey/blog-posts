---
aliases: []
categories: []
date-created: October 12, 2022 1:26 PM
Project: Recody
tags: [JPA, 개발 일반]
date-updated: 2023-02-27 06:03
---
<br><br>
# 상황

> 유저 (users table) 가 특정 작품 (content table) 에 별점을 부여한다.
> 
> - 이때 별점은 특정 유저가 작품에 단 1 번만 줄 수 있어야 하기 때문에 (유저 + 작품) 조합이 유일해야한다.
> - 별점 테이블은 rating 이라는 테이블을 만든다고 가정한다.
<br><br>
## 1. 복합키 — `@IdClass`

- db 지향적. 쿼리할 때 간편하다.
- 복합키 지정시 자동으로 조합을 인덱스로 생성한다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/38b03886cdf70acf4eddd6dd438d6f1d.png)
<br><br>
## 2. 복합키 — `@EmbeddedId`

- 객체지향적. 쿼리할 때 한단계 더 들어가야함.
<br><br>
## 3. 제약조건 — 두개 컬럼 조합에 Unique Constraint

- 이 때 rating 테이블의 id 는 임의의 UUID 등을 부여한다.
- 하나의 이벤트로 취급할 수 있다.
- 만약, 부여한 별점을 가지고 통계를 낸다거나 할 때 근거가 될 수 있다.
    - 복합키로 구현한다면 users + content 조합을 함께 제시해야하게 됨.
    - 예를 들어 작품 A 에 대해 모든 별점을 가져와 평균을 낸다고 하면, content A 로 쿼리해서 점수들을 가져올 수 있을 것이다. 복합키라고 하더라도 이건 문제가 안됨. 다만 개별 별점 레코드를 특정할 수 없을 뿐이다.
    - 어떤 작품에 10 만개의 별점이 있을 때 딱히 10 만개의 UUID 를 가져와서 특정해야할 만큼 중요하지 않을 수는 있다. 단지 점수들을 가져올 수 있기만 하면 되니까.

- 데이터가 많아진다.
    - UUID 는 자체로 큰 용량을 차지하게 된다. 하지만 거의 사용하지 않을 데이터일 가능성이 높다.
<br><br>
# rating 테이블을 참조하는 다른 테이블 (C) 이 있다면?
<br><br>
## 1. 복합키로 구현한 경우

- C 에서 rating 테이블의 id 를 구성하는 userId, contentId 를 각각 JoinColumn 으로 지정해주어야 한다.
- 아래 예시의 Participation 은 meeting_id, user_id 를 복합키로 갖는다.

```bash
@Entity
public class Payment {
    @Id
    @Column(name="id")
    @GeneratedValue(strategy=GenerationType.AUTO)
    private int id;
    @ManyToOne
    @JoinColumns({
            @JoinColumn(name="meeting_id"),
            @JoinColumn(name="user_id")
    })
    private Participation participation;

    // ...

    public Payment() {}
}
```