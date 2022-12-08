---
layout: post
title: "· Querydsl 사용자 정의 레포지토리 & 페이징"
nav_order: 10
parent : JPA
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/JPA/QuerydslRepo
---
# Querydsl 사용자 정의 레포지토리 & 페이징
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---




## 사용자 정의 레포지토리

사용자 정의 레포지토리는, JPARepository가 기본적으로 제공하는 메서드가 아닌, 구현부를 직접 작성해야할 때 필요하다.

특히, 조건문이 까다로운 동적 쿼리같은 경우는 `Querydsl`을 사용해서 직접 구현할 필요가 있다.

<br>

먼저 **JPARepository 를 상속받는 객체 레포지토리 인터페이스**를 생성한다.

객체 레포지토리에는 JPA 메서드 명명규칙에 따라 만들 수 있는 메서드들을 만든다.

```java
//예시

public interface MemberRepository extends JpaRepository<Member, Long> {

}
```



<br>

JpaRepository에 의해 자동으로 구현되는 메서드가 아닌, 사용자가 직접 원하는 구현체가 필요한 메서드는,

먼저 Custom 인터페이스를 하나 생성하고 추상메서드를 작성한다.

```java
// 예시

public interface MemberRepositoryCustom{
    List<Member> search(Condition condition);
        //Condition은 조건문으로 사용하고 싶은 객체들을 담아둔 사용자가 직접 정의한 객체
}
```

<br>

그리고 그 Custom을 **엔티티로 사용하는 객체 이름을 따서, [객체RepositoryImpl] 의 이름으로 `implements` 받은 뒤 구현부를 작성한다.**

```java
// 예시

public class MemberRepositoryImpl implements MemberRepositoryCustom{
    @Override
    List<Member> search(Condition condition){
        .....
    }
}
```

<br>

이제, 직접 구현한 클래스가 아닌, 인터페이스로 정의해놓은 MemberRepositoryCustom을 같이 상속받도록 설정해두면 된다.

```java
//예시

public interface MemberRepository extends JpaRepository<Member, Long>,MemberRepositoryCustom {

}
```


<br>


## 스프링 데이터 페이징 활용

<br>

스프링 데이터의 `Page` 와 `Pageable` 를 활용해야 한다!



```java
public interface MemberRepositoryCustom {
    //단순
    Page<객체> searchPageSimple(Condition condition, Pageable pageable);
    //복잡 (카운트 쿼리 분리)
    Page<객체> searchPageComplex(Condition condition, Pageable pageable);
}
```

<br>

구현체에서, 파라미터로 받은 `Pageable` 클래스의 메서드를 `queryfactory`와 함께 사용한다.

`fetch` 가 아닌 `.fetchResults()` 를 사용하면, 카운트 쿼리도 함께 실행된다.

```
.offset(pageable.getOffSet()) //몇번째 부터 시작할 것인가
.limit(pageable.getPageSize()) //몇개씩 나눌 것인가
.fetchResults();
```

<br>

```
List<객체> content = results.getResults();
long total = results.getTotal();

return new PageImpl<>(content,pageable,total);
```

`fetchResults`를 사용한 덕분에, 결과와

`PageImpl` 이라는 객체로 반환하면 되고, 파라미터를 잘 입력해주면 된다.

{: .highlight }
실제 이 메서드를 사용하는 방법은, `Pageable` 인터페이스를 구현한 `PageRequest` 객체를 활용해서, page와 limit 설정을 해주면 된다.

```
PageRequest pageRequest = PageRequest.of(0,3);
```

<br>

위와 같은 Pageable을 상속하는 PageRequest 클래스를 만들어 파라미터로 전달해주면,

3개씩 나눴을 떄, 0페이지 데이터를 가져올 수 있다.



{: .highlight }
혹은 count query 는 따로 queryfactory로 fetchCount() 해서 구하고, 그냥 결과는 fetch()로 가져오는 방법이 있다. 기본적으로 조인이 들어가면 count쿼리가 복잡해지기 때문이다.



```java
//count 쿼리와 분리한 예시

public Page<MemberDto> searchPageComplex (MemberSearchCondition condition, Pageable pageable){
            List<MemberTeamDto> content = queryFactory
                    .select(new QMemberTeamDto(
                            member.id,
                            member.username,
                            member.age,
                            team.id,
                            team.name))
                    .from(member)
                    .leftJoin(member.team, team)
                    .where(usernameEq(condition.getUsername()),
                            teamNameEq(condition.getTeamName()),
                            ageGoe(condition.getAgeGoe()),
                            ageLoe(condition.getAgeLoe()))
                    .offset(pageable.getOffset())
                    .limit(pageable.getPageSize())
                    .fetch();
      
            long total = queryFactory
                    .select(member)
                    .from(member)
                    .leftJoin(member.team, team)
                    .where(usernameEq(condition.getUsername()),
                            teamNameEq(condition.getTeamName()),
                            ageGoe(condition.getAgeGoe()),
                            ageLoe(condition.getAgeLoe()))
                    .fetchCount();
      
            return new PageImpl<>(content, pageable, total);
}
```

