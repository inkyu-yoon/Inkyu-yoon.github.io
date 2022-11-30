---
layout: post
title: "· Persistence Context 와 변경감지"
nav_order: 2
parent : JPA
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/JPA/PersistenceContext
---

# Persistence Context 와 변경감지
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



## 영속성 컨텍스트

<br>

영속성 컨텍스트란, **엔티티를 영구 저장하는 환경** 이라는 뜻이다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221130204857716.png" alt="image-20221130204857716" style="zoom:67%;" />
</p>


영속성 컨텍스트는, 엔티티 매니져를 통해서 접근할 수 있다.

`entityManager.persist(객체)` 를 통해서 객체는, 영속성 컨텍스트로 전송이 되고,

`transaction.commit()` 혹은 `entityManger.flush()` 혹은 `entityManager.createQuery()` 를 통해서 트랜잭션이 커밋되기 전에 DB로 전송시킬 수 있고, **DB에 저장하기 위해 INSERT 쿼리문이 실행된다.**  (💡영속성 컨텍스의 데이터를 비우면서 보내는 것이 아닌, 동기화의 의미이다.)

**DB로 전송하기 전까지 객체는, 기본키 값을 key값으로 하고 객체를 value값으로 하는 식으로 영속성 컨텍스트에 저장되게 된다.**(일종의 캐시 상태)

<br>

💡 `find` 시에는, 1차 캐시에서 먼저 key값에 해당하는 객체를 찾아보고, 없다면 DB에서 조회한 뒤, 캐시에 저장하고 반환한다.

따라서, db에 전송하기 전, 영속성 컨텍스트에 이미 있는 객체를 조회하는 경우에는 쿼리문 없이 객체를 가져올 수 있어서 쿼리문이 실행되지 않는다.

<br>

> 영속성 컨텍스트에서 가져오는 객체들은, 변수명을 달리해서 초기화 해도 같은 key값으로 가져왔다면 같은 객체임을 보장할 수 있다.



{: .highlight }
커밋 직전 까지는 쿼리문이 계속 쌓이기 때문에 batch size를 설정하면, 일정 수량 만큼 쌓였을 시 쿼리문을 전송하는 기능을 설정할 수 있다.

<br>

---



## 변경감지

<br>

JPA는 DB에서 가져온 객체를 변경하면, DB에 적용된다.

즉, 객체의 데이터 변경 뒤, persist와 같은 동작을 진행할 필요가 없다.

DB에서 데이터를 가져올 때, **가져온 시점에서의 객체를 저장해두고, 이와 비교해서 변경된 것이 있는지 감지한다.**

그렇게 JPA는 객체 데이터가 **변경되었다는 것을 감지하면, transaction 커밋 시 Update 쿼리를 실행하게끔 설계되어 있다.**