---
layout: post
title: "· Querydsl Projection 과 동적 쿼리"
nav_order: 8
parent : JPA
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/JPA/QuerydslProjection
---
# Querydsl Projection 과 동적 쿼리

{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---




## Projection (원하는 속성 데이터만 가져오고 싶을 때)

<br>

### 속성 명 지정

<br>

```java
    @Test
    public void projection() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        List<Tuple> fetch = queryFactory.select(member.username, member.age)
                .from(member)
                .fetch();
        for (Tuple tuple : fetch) {
            System.out.println(tuple.get(member.username));
            System.out.println(tuple.get(member.age));
        }
    }
    
    // 속성 여러개 select 하는 경우는 tuple 로 반환
    @Test
    public void projection2() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        List<Tuple> fetch = queryFactory.select(member.username, member.age)
                .from(member)
                .fetch();

        for (Tuple tuple : fetch) {
            System.out.println("tuple = " + tuple);
            System.out.println("tuple.get(member.username) = " + tuple.get(member.username));
            System.out.println("tuple.get(member.username) = " + tuple.get(member.age));
        }

    }
```

`select` 메서드 안에 추출하길 원하는 속성 명을 쓰고 `from` 에는 객체 이름만 작성하여 가져오면 된다.

참고로 `Tuple` 객체를 직접 다루지 말고, DTO로 반환하는 것이 좋다.

<br>



### DTO 로 초기화해서 가져오기

<br>

예를 들어서, `Member` 엔티티는 이름, 나이, 팀 속성을 갖고 있을 때, 이름과 나이만 갖는 DTO 로 변환한다고 가정해보겠다.

```java
@Getter
@AllArgsConstructor
public class MemberDto {

    private String name;
    private int age;
}

```

위와 같은, 생성자가 있는  DTO가 있다.

<br>



```java
    @Test
    public void dto() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);

        List<MemberDto> fetch = queryFactory.select(Projections.constructor(MemberDto.class, 						member.username, member.age))
                .from(member)
                .fetch();

        for (MemberDto memberDto : fetch) {
            System.out.println("memberDto = " + memberDto.getName());
            System.out.println("memberDto = " + memberDto.getAge());
        }
    }
```

`Projections.constructor(DTO객체, 생성자 파라미터)` 이와 같은 형식으로 구성하면 된다.

bean 방식, fields 방식도 있는데 `constructor` 방식이 제일 직관적으로 이해하기 쉽고 안전한 방법 같다.


<br>


## 동적 쿼리 (BooleanBuilder)

<br>

### BooleanBuilder 방식

<br>

```java
    @Test
    public void booleanBuilder() {
        String usernameParam = "이강인";
        Integer ageParam = 10;
        List<Member> result = searchMember1(usernameParam, ageParam);

        for (Member member1 : result) {
            System.out.println("member1.getUsername() = " + member1.getUsername());
        }
    }

    //둘 다 null 일 경우 기본, 하나만 null 일경우 하나만 검색하게 된다.

    private List<Member> searchMember1(String usernameCond, Integer ageCond) {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);

        BooleanBuilder builder = new BooleanBuilder();
        if (usernameCond != null) {
            builder.and(member.username.eq(usernameCond)); //null이 아니면 username 조건 추가
        }

        if (ageCond != null) {
            builder.and(member.age.goe(ageCond));
        }
        
        return queryFactory.select(member)
                .from(member)
                .where(builder)
                .fetch();
    }
```

`BooleanBuilder` 객체를 생성해서, 파라미터 값 조건에 따른 조건문을 `.and()` 혹은 `.or()` 등으로 메서드로 추가해준다.

완성된 `builder` 는 `where` 메서드에 넣는다.

<br>



### Where 다중 파라미터 방식



<br>

```java
    @Test
    public void dynamic_Query() {
        String userName = null;
        Integer age = 20;
        List<Member> members = search(userName, age);
        for (Member member1 : members) {
            System.out.println("member1.getUsername() = " + member1.getUsername());
        }
    }

    private List<Member> search(String userNameCon, Integer ageCon) {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        return queryFactory.selectFrom(member)
                .where(userNameEq(userNameCon),ageEp(ageCon))
                .fetch();
    }

    private Predicate ageEp(Integer ageCon) {
        return ageCon == null ? null : member.age.loe(ageCon);
    }

    private Predicate userNameEq(String userNameCon) {
        return userNameCon==null? null : member.username.eq(userNameCon);
    }
```

where 조건문에 들어가는 조건을, 메서드로 새로 생성해서 조건을 작성한다.

각 메서드는 `Predicate` 클래스로 반환되도록 하면 된다.

<br>

```java
    private BooleanExpression ageEp(Integer ageCon) {
        return ageCon == null ? null : member.age.loe(ageCon);
    }

    private BooleanExpression userNameEq(String userNameCon) {
        return userNameCon==null? null : member.username.eq(userNameCon);
    }
    
    private BooleanExpression ageAndName(String userNameCon, Integer ageCon) {
        return userNameEq(userNameCon).and(ageEp(ageCon));
    }
```



조합하고 싶다면, 단건은 `BooleanExpression` 클래스로 반환하도록 메서드를 만든 뒤, retrun을 and로 묶어서 반환하는 메서드를 생성하면 된다.

`queryFactory` 문이 간결해지는 장점이 있다.



<br>



### update, delete 벌크 연산 적용 (데이터 여러개 한번에 변경 적용 시)

<br>

```java
    @Test
    public void bulk() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        long count = queryFactory.update(member)
                .set(member.username, "비회원")
                .where(member.age.lt(28))
                .execute();

    }
    
    @Test
    public void bulk2() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        long count = queryFactory.update(member)
                .set(member.age, member.age.add(1))
                .execute();

    }
    
    @Test
    public void blukDelete() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        queryFactory.delete(member)
                .where(member.username.eq("member1"))
                .execute();
                
    }
```

{: .important}
> `execute` 해서 DB에 적용되었다.
>
> 벌크 연산 수행 후에는, 실제 DB에는 변경되었고 영속성 컨텍스트는 이러한 변경을 감지하지 못한 상태가 되어 flush와 clear를 해도 DB에 아무런 변화가 생기지 않는다.
>
> 하지만 flush와 clear를 해주지 않게 되면 벌크 연산 전의 엔티티 상태가 영속성 컨텍스트에 존재하기 때문에, 영속성 컨텍스트에도 변경 사항을 반영하고 싶다면 `em.flush()` 와 `em.clear()` 해서 영속성 컨텍스를 비워주는 것이 좋다.

<br>

이러한 벌크 연산은, `update` `set` 조합을 적절히 활용하는 것이 중요하다.



<br>

## JPQQueryFactory 스프링 Bean 으로 등록

<br>

```java
@Bean
JPAQueryFactory jpaQueryFactory(EntityManager em){
return new JPAQueryFactory(em);
}
```

Querydsl 사용을 위한 JPAQueryFactory 클래스를,

위와 같이 빈으로 등록하여 주입받은 뒤, `@RequiredArgsConstructor` 와 함께 사용하는 방법도 있다.





