---
layout: post
title: "· Querydsl 기본적인 쿼리문 작성하기"
nav_order: 6
parent : JPA
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/JPA/QuerydslCreateQuery
---

# Querydsl 기본적인 쿼리문 작성하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## Querydsl where 파라미터로 사용할 수 있는 메서드 정리

<br>

| 메서드          | 기능                                             |
| --------------- | ------------------------------------------------ |
| eq()            | 값이 같을 때                                     |
| ne()            | 값이 아닐 때                                     |
| isNotNull()     | null 값이 아닐 때                                |
| in(a,b,c ..)    | 값이 a,b,c,...와 같을 때                         |
| notIn(a,b,c ..) | 값이 a,b,c, ... 가 아닐 때                       |
| between(a,b)    | 값이 a이상 b이하일 때                            |
| goe(a)          | greater or equal : a 이상일 때                   |
| loe(a)          | lower or equa : a 이하일 때                      |
| gt(a)           | greater than : a 보다 클 때                      |
| lt(a)           | lower than : a 보다 작을 때                      |
| like()          | sql like 검색                                    |
| contains()      | like "%문자열%" 와 같은 기능, 문자열을 포함할 때 |
| startsWith()    | like "문자열%" 와 같은 기능, 문자열로 시작할 때  |



## fetch 메서드 종류

<br>

| 메서드         | 기능                                                         |
| -------------- | ------------------------------------------------------------ |
| fetchOne()     | 단건 조회(결과가 여러개일 경우 exception 발생)               |
| fetch()        | 결과를 List로 반환                                           |
| fetchFirst()   | 제일 첫번째 데이터 단건 반환                                 |
| fetchResults() | count 쿼리 까지 같이 실행시켜서(쿼리를 두번 실행), getTotal() 메서드도 사용 가능 |
| fetchCount()   | count 쿼리만 실행시켜서 `long` 으로 반환                     |





## Querydsl 정렬과 페이징 예시

<br>

```java
 List<Member> members = queryFactory.selectFrom(member)
                .orderBy(member.username.desc())
                .offset(1)
                .limit(2)
                .fetch();
```

`.orderBy(정렬 기준 속성.정렬방법)` 과 같이 사용한다.

offset은 시작 데이터의 위치를 의미하고, limit는 데이터를 어떤 갯수로 나눌지를 의미한다.

위와 같은 예시 조건은 내림차순 정렬 뒤, offset(1) 이므로 데이터 1번째부터 2개를 반환한다.(데이터는 0번째부터 시작)

> orderBy(조건1,조건2) 와 같이 파라미터를 여러개 설정하여 우선순위를 정할 수 있다.



## 단순 응용 메서드 기능 (count, max, min, avg, sum)

<br>

| 메서드  | 기능   |
| ------- | ------ |
| count() | 개수   |
| sum()   | 총 합  |
| avg()   | 평균   |
| max()   | 최댓값 |
| min()   | 최솟값 |



### 예시

<br>

```java
  public void aggregation() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        List<Tuple> result = queryFactory.select(
                        member.count(), 
                        member.age.sum(),
                        member.age.avg(),
                        member.age.max(),
                        member.age.min())
                .from(member)
                .fetch();

        Tuple tuple = result.get(0);
        Assertions.assertThat(tuple.get(member.count())).isEqualTo(4);
        Assertions.assertThat(tuple.get(member.age.sum())).isEqualTo(100);
        Assertions.assertThat(tuple.get(member.age.avg())).isEqualTo(25);
        Assertions.assertThat(tuple.get(member.age.max())).isEqualTo(40);
        Assertions.assertThat(tuple.get(member.age.min())).isEqualTo(10);
    }
```

`Tuple` 클래스로 반한되고, `get` 메서드를 통해서 원하는 값을 추출할 수 있다.

물론, 실제로는 Tuple을 직접 다루지 않고, DTO로 변환해서 사용한다.



## 그룹화 기능

<br>


일반적으로 그룹화기능은 1대 다 연관 관계가 있는 릴레이션 사이에서,

두 릴레이션을 join 한 뒤,

특정 조건을 기준으로 데이터를 그룹화해서 총합을 구하거나 통계를 구할 때 사용한다.

```java
	   Team teamA = new Team("대한민국");
        Team teamB = new Team("일본");
        em.persist(teamA);
        em.persist(teamB);

        Member member1 = new Member("이강인", 10, teamA);
        Member member2 = new Member("손흥민", 20, teamA);

        Member member3 = new Member("쿠보", 30, teamB);
        Member member4 = new Member("도안리츠", 40, teamB);
        em.persist(member1);
        em.persist(member2);
        em.persist(member3);
        em.persist(member4);
```

데이터베이스에 위와 같이 데이터가 있다고 가정할 때,

팀 이름을 기준으로 그룹화 한뒤, 선수들의 평균 나이를 구해보면

```java
void getAgeAvg() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);


        List<Tuple> tuples = queryFactory.select(team.name, member.age.avg())
                .from(member)
                .join(member.team, team)
                .groupBy(team.name)
                .fetch();

        for (Tuple tuple : tuples) {
        System.out.println("tuple = " + tuple);
        }
        
    }
```

위와 같이 `member.team` 과 `team` 을 조인한 뒤(QMember와 QTeam) , `team.name` 으로 그룹화 하면 된다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221204183649815.png" alt="image-20221204183649815" style="zoom:80%;" /></p>

원하는 결과를 얻을 수 있었다.

> `.having()` 을 이용해서 그룹화하는 과정에 조건을 추가할 수도 있다.
> 
> .having(team.name.eq("대한민국"))

