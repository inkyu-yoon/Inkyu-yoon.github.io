---
layout: post
title: "· Spring Weaving(RTW · PCW · CTW · LTW)의 특징"
nav_order: 12
parent : Spring
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Spring/spring-aop-weaving
---

# Spring Weaving(RTW · PCW · CTW · LTW)의 특징
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## AOP 란?

AOP(Aspect Oriented Programming)은 ***관점 지향 프로그래밍***으로 불린다.

해당 메서드에서 개발자가 집중해야할 핵심로직이라고 할 수 없는 부가 기능을 모듈화하여 핵심 로직가 분리하는 것이 목표이다.

AOP를 통해, 개발자는 핵심 로직에만 집줄할 수 있으며, 핵심 로직에 대한 테스트를 이전보다 쉽게 할 수 있고 코드에 대한 가독성을 올려준다.

대표적인 예시로, `@Transactional` 어노테이션이 있다.

<br>

스프링 AOP는 다양한 방식의 AOP를 지원한다.

대표적으로 CTW(Compile-Time Weaving), PCW(Post-Compile Weaving), LTW(Load-Time Weaving), RTW(Runtime Weaving)가 있다.

<br>

## Weaving 이란?

***PointCut***에 의해서 결정된 타켓에 ***Advice***(부가기능)을 삽입하는 과정

> 📌 Advice : 어드바이스는 타겟에 제공할 ***부가기능을 담고 있는 모듈***이다.
>
> 📌 Pointcut : 어드바이스를 적용할 ***타겟의 메서드를 선별하는 정규표현식***이다. 부가 기능을 적용할 대상인지 아닌지를 판별해주는 필터링 로직이다.

<br>

### PCW(Post-Compile Weaving)

PCW는 ***컴파일 이후***에 컴파일된 클래스 파일에 대해 ***별도의 프로세스를 통해 바이트 코드를 수정해 AOP 기능을 적용하는 방식***이다.

>  소스 코드(`.java`) 작성 →  컴파일하여 바이트 코드(`.class`) 생성 → Weaving 작업

<br>

### CTW (Compile-Time Weaving)

***컴파일 시점***에서 ***AOP 코드를 대상 코드에 직접 주입하는 방식***이다.

CTW를 사용하면 AOP 관련 코드가 소스 코드에 미리 삽입되어 ***컴파일된 클래스 파일에 포함***된다.

> 소스 코드(`.java`) 작성 → Weaving 작업 →  컴파일하여 바이트 코드(`.class`) 생성

<br>

### LTW (Load-Time Weaving)

컴파일 시점이나 클래스 로딩 이전에 바이트 코드에 AOP 어드바이스 코드를 주입하는 것과는 달리,

***클래스 로딩 시점에 동적으로 Weaving을 수행하는 방식***이다.

따라서, 바이트 코드에 직접적인 수정을 가하지 않고 AOP를 적용할 수 있다.

하지만, 컴파일 단계나 클래스 로딩 이전에 바이트 코드를 변경하는 CTW와 PCW에 비해 성능상의 오버헤드가 발생할 수 있다.

>  소스 코드(`.java`) 작성 →  컴파일하여 바이트 코드(`.class`) 생성 → Weaving 작업

<br>

### RTW (Runtime Weaving)

RTW는 ***런타임 시점에 동적으로 Weaving을 수행하는 방식***으로,

클래스 파일이나 클래스 로더를 수정하지 않고도 프로그램 실행 중에 AOP를 적용한다.

***런타임에 프록시 객체를 생성하여 대상 객체를 감싸는 방식으로 AOP 를 적용***하는 것이고,

Spring AOP 는 기본적으로 RTW 방식을 선호한다.

<br>

**JDK 다이나믹 프록시** 또는 **CGLIB 프록시를** 사용하여 RTW를 지원한다.

> 1. 프로그램 실행 중에, RTW 프레임워크는 AOP를 적용할 시점을(메서드 호출 · 예외 발생 등) 탐지한다.
> 2. 원본 객체와 동일한 인터페이스를 구현한 동적 프록시를 생성하여 원본 객체를 감싸고, 메서드를 가로채는 코드를 포함한다.
> 3. 동적 프록시가 가로챈 메서드가 호출될 때, 해당 메서드를 실행하기 전에 지정된 Advice(부가 기능)을 실행한다.
> 4. Advice 실행이 완료되면, 동적 프록시가 가로챈 메서드를 원본 객체에 전달하여 원래의 동작을 수행한다.



