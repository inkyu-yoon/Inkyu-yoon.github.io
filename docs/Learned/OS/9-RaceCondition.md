---
layout: post
title: "· Race Condition과 Mutex · Semaphore"
nav_order: 9
parent : 운영체제
grand_parent: 📚Learned
permalink: docs/Learned/OS/RaceCondition
---

# Race Condition과 Mutex · Semaphore
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


{: .important-title}
> Race Condition 언어 핵심
>
> Race Condition(경쟁 상태)란 두 개 이상의 프로세스가 공유 자원에 동시 접근할때, 접근 순서에 따라 실행 결과가 달라지는 현상을 말한다.
> 
> 따라서, 공유 자원에 접근할 수 있는 스레드에 제한을 걸어 놓는 것이 중요한데, 이를 상호배제라고 한다.
> 
> 대표적인 방식으로는, Mutex와 Semaphore가 있다. 
> 
> 두 방식의 공통점은 접근 권한이 없으면 쓰레드는 접근 권한을 얻을때까지 대기해야한다는 점이고
> 
> 차이점은 Mutex는 임계 구역에 진입하여 권한(key)를 획득한 쓰레드 만이 락을 해제할 수 있지만, 
> 
> 세마포어는 임계구역에 진입하지 않은 쓰레드도 signal 함수를 통해 락을 해제할 수 있다.

<br>

## Race Condition이란

***Race Condition(경쟁 상태)*** 란 두 개 이상의 프로세스가 **공유 자원에 동시 접근할 때**, 실행 순서에 따라 **결과값이 달라질 수 있는 현상**을 말한다.

따라서, 공용 자원은 하나의 쓰레드만 사용할 수 있도록 **임계구역으로 설정**해두고, 한 쓰레드가 사용중이라면 다른 쓰레드는 **접근하지 못하도록 상호배제**시키는 방법으로 해결할 수 있다.

<br>

## 해결책



### Mutex(뮤텍스)

Mutex는 **Mut**ual **Ex**clusion(상호배제)의 약자이다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230411174243759.png" alt="image-20230411174243759" style="zoom: 40%;" />
</p>

뮤텍스는 Locking 매커니즘으로, 하나의 쓰레드 만이 뮤텍스를 얻어 임계 구역에 들어올 수 있고,

**뮤텍스를 획득했던 쓰레드만이 뮤텍스를 해제할 수 있다.**

따라서, 다른 쓰레드는 뮤텍스를 해제할때까지 임계구역에 접근하지 못한다.

<br>

### Semaphore(세마포어)

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230411173919118.png" alt="image-20230411173919118" style="zoom: 50%;" />
</p>

카운팅 세마포어의 경우, **설정한 값만큼 쓰레드를 허용하고 그 이상의 쓰레드가 자원에 접근하면 락이 실행된다.**

즉, 공통으로 관리하는 하나의 값으로 상호배제를 달성한다.

<br>

참고로, 허용하는 쓰레드가 1개인 세마포어를 바이너리 세마포어라 하고 이는 뮤텍스와 동일하게 동작한다.

<br>

세마포어는 공유 자원에 접근하기 위해 카운팅 변수를 감소시키고 대기하는 wait 함수와

임계 구역을 빠져나오면서 변수를 증가시키는 signal 함수를 이용해 관리한다.

<br>

세마포어는 signal 함수로 락을 걸지 않은 쓰레드도 락을 해제할 수 있다는 점에서 뮤텍스와는 차이점이 존재한다.

<br>

{: .highlight-title }
> 참고한 블로그
>
> 1. [https://chelseashin.tistory.com/40](https://chelseashin.tistory.com/40)