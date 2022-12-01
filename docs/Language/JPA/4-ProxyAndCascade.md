---
layout: post
title: "· JPA 프록시와 영속성 전이"
nav_order: 4
parent : JPA
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/JPA/ProxyAndCascade
---

# JPA 프록시와 영속성 전이
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 프록시

<br>

### JPA의 데이터베이스 접근 메커니즘 (쿼리 실행 조건)

---

JPA가 데이터베이스에 있는 테이블에 접근하는 메커니즘을 간단하게 설명해보겠다.

객체 간(데이터베이스 상 테이블 간) 연관 관계가 되어 있을 때,

연관되어 있는 객체의 정보를 사용하지 않는 이상 연관된 객체(테이블)에 대해서는 쿼리를 실행시키지 않고 임시 객체 상태로서 보관하고 있고, 이 가짜 객체 상태를 **프록시**라고 한다.

프록시가 사용자의 요청에 의해서, 진짜 객체 데이터로 채워지면 엔티티 객체로 완전히 바뀌는 것이 아니기 때문에 **타입 체크시 `==` 이 아닌 `instance of` 를 사용해야 한다!!**

<br>

{: .important}

> 예를 들어, **Member** 테이블과 **Team** 테이블이 서로 연관되어 있고, **Member** 클래스 안에 `team` 이라는 변수명으로 **Team**과 매핑되어 있을 때,
>
> 연관되어 있는 다른 객체를 프록시로 가져오고 싶다면 `@XToOne(fetch = FetchType.LAZY)` 참조된 객체에 어노테이션 설정을 추가해주어야 한다.
>
> `XToMany` 는 디폴트 설정이 LAZY라서 명시하지 않아도 된다.
>
> 이를 지연로딩 설정이라고 하고, 지연 로딩 설정을 하게 되면
>
> **Member** 의 **Team**과 연관되어있지 않은 변수를 사용할때는, 데이터베이스에서 **Member** 테이블에만 접근을 하고,
>
> **Member** 가 가진 `team` 객체의 외래키가 아닌 데이터를 사용하려고 할때, 그제서야 데이터베이스의 **Team** 테이블에 접근하고 프록시에 데이터를 채운다.
>
> 쿼리문 실행을 최소화 시키려는 시스템이라고 볼 수 있다.

<br>



## 영속성 전이

<br>

- 연관된 객체를 영속성 컨텍스트에 저장하고 싶을 때 사용

```
	  // 구문
        Child child1 = new Child();
        child1.setChildName("son");
        child1.setChileAge("1");

        Child child2 = new Child();
        child2.setChildName("daughter");
        child2.setChileAge("2");

        Parent parent = new Parent();
        child1.addParent(parent);
        child2.addParent(parent);

        em.persist(child1);
        em.persist(child2);

        em.flush();
        em.clear();
        
        //Child 클래스의 연관관계 메서드
        
        public void addParent(Parent parent) {
        this.parent = parent;
        parent.getChilds().add(this);
    	}
```

위와 같은 구문이 있다고 가정해보자.

영속성 전이 설정`(cascade = CascadeType.ALL)` 이 되어 있지 않으면 에러를 발생시킨다.

왜냐하면, `child1`, `child2` 객체만 영속성 컨텍스트로 persist 되었고 `parent` 는 persist 되지 않았기 때문에,

DB에서 child 관련 쿼리문만 실행되고, 결국엔 parent 테이블 외래키 값이 null이 되면서 에러를 발생시키게 되는 것이다.

**따라서, em.persist(parent) 해주던가 영속성 전이 설정(`(cascade = CascadeType.ALL)` )을 해주어야 한다.**