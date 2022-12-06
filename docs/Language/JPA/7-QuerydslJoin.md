---
layout: post
title: "· Querydsl Join & 서브쿼리 & Concat"
nav_order: 7
parent : JPA
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/JPA/QuerydslJoin
---
# Querydsl Join & 서브쿼리 & Concat

{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

## Join

<br>

`join` 사용시, 파라미터로 `(Q객체1.연관 관계가 맺어진 변수명 , Q연관 관계맺어진 객체)` 를 파라미터로 입력하면 된다.

`join` 을 사용하면 기본적으로 **inner join** 이 사용되며, 외래키가 null 값인 경우는 제외한다.

`left join` 과 같은 외부 조인을 사용하고 싶다면 `leftjoin()` 을 사용하면 되고, `on()` 을 사용해서 조인하는 테이블에 조건을 추가할 수 있다.

```java
    /*
    회원과 팀을 조인하면서 팀 이름이 '대한민국' 팀만 조인, 회원은 모두 조회
    select m, t from Member m left join m.team t on t.name = 'teamA'
     */

    public void join() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);

        List<Tuple> tuples = queryFactory.select(member, team)
                .from(member)
                .leftJoin(member.team, team)
                .on(team.name.eq("대한민국"))
                .fetch();

        for (Tuple tuple : tuples) {
            System.out.println("tuple = " + tuple);
        }

    }
```

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221204182414903.png" alt="image-20221204182414903" style="zoom:80%;" />
</p>

결과는 위와 같이 나왔다. left join을 했기 때문에, 모든 선수들의 데이터를 포함하고, team 이름이 대한민국인 선수만 join이 되었다.

그냥 join을 하게 되면,

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221204182523077.png" alt="image-20221204182523077" style="zoom:80%;" />
</p>

위와 같은 결과로 나타날 것이다.



외부 조인이 아닌 조인도 `on` 을 사용해도 되지만, `where` 조건을 사용하는 것과 결과는 같을 것이다.

<br>

## Fetch Join

<br>

페치조인은 지연로딩 시 연관관계가 맺어진 객체가 가진 데이터를 사용할 때마다 쿼리문을 실행시키게 되는 상황에서 성능 최적화를 하기 위한 선택이다.

연관된 엔티티를 한번의 쿼리로 다 가져와서, 여러번 쿼리문을 실행하는 것을 방지한다.

```java
@Test
    public void fetchJoin() {
        em.flush();
        em.clear();
        JPAQueryFactory queryFactory = new JPAQueryFactory();
        Member findMember = queryFactory.selectFrom(member)
                .join(member.team,team).fetchJoin()
                .where(member.username.eq("member1"))
                .fetchOne();
    }

```

fetch join 은 기존의 join 뒤에 fetchJoin() 을 붙여준다.


<br>


## 서브쿼리

<br>

```java
     /**
     * 나이가 가장 많은 회원 조회
     */
    public void subQuery() {
        QMember subMember = new QMember("subMember");

        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        queryFactory.selectFrom(member)
                .where(member.age.eq(
                        JPAExpressions.select(subMember.age.max())
                                .from(subMember)
                ))
                .fetch();
    }
    
     /**
     * 나이가  평균 이상인 회원
     */
    public void subQuery2() {
        QMember subMember = new QMember("subMember");

        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        queryFactory.selectFrom(member)
                .where(member.age.goe( 
                        JPAExpressions.select(subMember.age.avg())
                                .from(subMember)
                ))
                .fetch();
    }
    
```

`JPAExpressions` 를 사용해서 subQuery를 만들어주면 되고, 서브 쿼리 내에서 이미 사용한 Q객체를 사용했다면, 구별을 위해서 새로운 Q객체를 생성해주어야 한다.

<br>

## Case문 (When, Then)

<br>

```java
	@Test
    public void basicCase() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        List<String> result = queryFactory
                .select(member.age
                        .when(10).then("열살")
                        .when(20).then("스무살")
                        .otherwise("기타"))
                .from(member)
                .fetch();

        for (String s : result) {
            System.out.println("s = " + s);
        }
    }
    
    @Test
    public void complexCase() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        List<String> result = queryFactory
                .select(new CaseBuilder()
                        .when(member.age.between(0.20)).then("0~20살")
                        .otherwise("기타"))
                .from(member)
                .fetch();

        for (String s : result) {
            System.out.println("s = " + s);
        }
    }
```

조금 복잡한 조건을 사용할 경우, `CaseBuilder()` 사용해서 querydsl 문법을 사용해서 조건문을 작성한다.

<br>

## 상수 혹은 문자 더하기

<br>

```java
@Test
    public void constant() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        List<Tuple> result = queryFactory
                .select(member.username, Expressions.constant("A"))
                .from(member)
                .fetch();

        for (Tuple tuple : result) {
            System.out.println("tuple = " + tuple);
        }
    }
```

결과값

```
tuple = [member1, A]
tuple = [member2, A]
tuple = [member3, A]
tuple = [member4, A]
```



데이터를 가진 새로운 속성을 추가하는 것과 비슷한 결과를 나타낸다.

SELECT 절에서 `Expressions.constant` 를 사용한다.


<br>


```java
 @Test
    public void concat() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        //{username}_{age}
        List<String> result = queryFactory
                .select(member.username.concat("_").concat(member.age.stringValue()))
                .from(member)
                .where(member.age.goe(10))
                .fetch();

        for (String s : result) {
            System.out.println("s = " + s);
        }
    }
```

결과값

```java
s = member1_10
s = member2_20
s = member3_30
s = member4_40
```



`concat()` 메서드를 활용해서 원하는 문자열을 합쳐서 결과를 나타내고, 문자열이 아닌 타입은 `stringValue()` 메서드를 활용한다.





