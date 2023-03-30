---
layout: post
title: "· Managed · UnManaged 언어의 특징"
nav_order: 8
parent : 운영체제
grand_parent: 📚Learned
permalink: docs/Learned/OS/ManagedUnManaged
---

# Managed · UnManaged 언어란?
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


{: .important-title}
> Managed-UnManaged 언어 핵심
>
> 가상 머신과 같은 런타임 환경 실행되기 때문에 특정 OS에 의존하지 않고 GC(가비지 컬렉터)와 같은 메모리 관리를 해주는 언어를 **Managed 언어**라고 하고
>
> 운영체제에 의해 바로 바이너리 코드로 컴파일 되어 하드웨어에서 실행되는 언어를 **Unmanged 언어**라고 한다.

<br>


## Managed 언어란?

<br>

코드가 하드웨어에서 바로 구동되는 것이 아니라 특정 런타임 환경에 의해 관리되고 의존하는 코드를 말한다.

자바에서는 JVM이 런타임 환경이라고 할 수 있다.

주의할 점은, GC가 있어서 Managed 언어인 것이 아니라

가상 머신 위에서 실행되기 때문에 Managed 언어이고

보통 가상 머신에는 GC가 있기 때문에 메모리 관리를 받을 수 있는 것이라고 한다.

즉 GC의 유무가 Managed - Unmanaged를 나누는 기준은 아니라고 한다.



### 장점

<br>

- 이런 런타임 환경에서 실행되는 코드는 언어차원에서 메모리 관리를 해준다는 장점이 있다.

- 코드가 런타임 환경에 의존하기 때문에 하드웨어나 OS에 종속되지 않는다.

<br>

### 단점

<br>

- 메모리를 구체적으로 관리할 수 없고 메모리 정리 시점이 비정기적이다.


<br>

## UnManaged 언어란?

<br>

Managed 언어처럼 IL(중간 언어)로 변환될 필요 없이 바로 운영체제가 이해할 수 있는 바이너리 코드로 컴파일 되어 실행되는 언어이다.

중간 과정이 없기 때문에 속도는 Managed 언어보다 빠르다.

하지만, 프로그래머가 메모리 관리를 주도적으로 해야한다.



### 장점

<br>

- 가상머신을 거치지 않기 때문에 속도가 빠르다.

- 메모리를 구체적으로 관리할 수 있다.

<br>

### 단점

<br>

- 메모리 관리를 잘 못하면 누수가 발생할 수 있다.

<br>


{: .highlight-title }
> 참고한 블로그
>
> 출처 : https://m.blog.naver.com/oh-mms/222048414302