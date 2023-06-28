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
> 핵심
>
> Managed 언어는 말 그대로 관리받는 언어라는 뜻입니다. 
> 
> Managed 언어는 가상머신과 같은 런타임 환경에서 실행되어 운영체제와 독립적이며 메모리 관리를 개발자가 직접 신경쓰지 않아도 된다는 장점이 있습니다.
> 
> 
> Unmanaged 언어는 가상머신과 같은 런타임 환경에서 실행되는 것이 아니고 운영체제와 직접 상호작용하는 언어입니다.
> 
> 따라서, 개발자가 직접 메모리 관리를 신경써야합니다. 


<br>


## Managed 언어란?

<br>

코드가 하드웨어에서 바로 구동되는 것이 아니라 특정 런타임 환경에 의해 관리되고 의존하는 코드를 말한다.

자바에서는 JVM이 런타임 환경이라고 할 수 있다.

JVM과 같은 가상 머신에는 GC가 있기 때문에 메모리 관리를 받을 수 있다.


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