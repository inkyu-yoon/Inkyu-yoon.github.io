---
layout: post
title: "· JAVA volatile과 Atomic 변수 · CAS"
nav_order: 22
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/Volatile
---

#  JAVA volatile과 Atomic 변수 · CAS
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

{: .important-title}
> volatile 핵심!
> 
> 쓰레드는 성능 향상을 위해 매번 Main Memory를 사용하는 것이 아닌 Cpu Cache를 사용하여 작업을 진행한다.
> 
> 멀티 쓰레드 환경에서는 Cache 데이터를 사용하면, 다른 스레드가 데이터를 변경해 Main Memory에는 반영되었지만 알아채지 못하는 상황이 발생하고
> 
> 이를 가시성 문제라고 한다. 자바에서는 Cache를 사용하지 않고 Main Memory만을 사용하도록 하는 volatile 키워드로 이 문제를 해결한다.
> 
> 하지만, volatile로 원자성 문제를 해결할 수 없어 결과적으로 동시성 문제도 해결할 수 없게되는데, 
> 
> 이때 사용할 수 있는 것은 non-blocking 기반 동기화 작업인 CAS 알고리즘을 사용하는 Atomic 변수이다.

<br>

## 가시성 문제란?

가시성 문제란, 하나의 스레드에서 공유자원을 수정한 결과가 다른 스레드에게 보이지 않는 문제를 말한다.

아래 예시를 살펴보자.

<br>

```java
public class Test {
    public static boolean flag = false;
    
    private static class A extends Thread{
        @Override
        public void run() {
            while(!flag){}
            System.out.println("완료!");
        }
    }
    public static void main(String[] args) throws InterruptedException {
        final A a = new A();
        a.start();

        Thread.sleep(200);
        flag = true;
        a.join();
    }
}
```

A 스레드를 실행시키면, 공유 자원인 flag 가 false 값이기 때문에 while 문을 순회하게 된다.

그러다가 메인 스레드에서 flag를 true로 변경하면 whlie문을 탈출할 것이라고 생각된다.

하지만, 실제로는 while 문을 탈출하지 못하는데 이러한 문제를 **가시성 문제**라고 한다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230410205547361.png" alt="image-20230410205547361" style="zoom:80%;" />
</p>


멀티 쓰레드에서 쓰레드는 성능 향상을 위해 **Main Memory에서 읽은 변수 값을 CPU Cache에 저장하게 된다.**

<br>

즉, 위 예시에서 A 스레드는 처음에 메인 메모리에서 flag 값을 false로 읽어온 뒤, 캐싱한 상태이기 때문에

다른 쓰레드가 **flag 값을 true로 변경해도 알아채지 못하고 while문을 빠져나오지 못하게 되는 것**이다.

<br>

💡 이러한 가시성 문제를 해결해 줄 수 있는 키워드가 ***volatile*** 이다.

<br>

공유 자원에 ***volatile*** 키워드를 사용하면, **항상 Main Memory에 읽고 쓰겠다는 것을 명시하는 것**이다.

`public volatile static boolean flag = false;` 로 첫 예시를 실행시키면 정상적으로 while문을 빠져나오는 것을 확인할 수 있다.

<br>

⚠️ 단, ***volatile*** 키워드는 가시성 문제를 해결하지만, 동시성 문제는 해결하지 못한다.

<br>

```java
public class Test {
    private static int t;
    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < 100; i++) {
            new Thread(() -> {
                for (int j = 0; j < 1000; j++)
                    System.out.println(t++);
            }).start();
        }
        Thread.sleep(1000);
        System.out.println("완료!");
    }
}
```

위 코드는, t라는 공유 자원을 100개의 스레드가 각각 1000번 for 문을 돌면서 t를 1씩 증가시킨다.

기대하는 결과는 100000이 나와야 하지만 실제로는 99987 과 같이 100000만보다 작은 수가 나온다.

그 이유는, ① 공유 변수를 읽고, ② 수정하고, ③ 메모리에 저장 하는 과정을 거치는데

다른 쓰레드가 메모리에 변경된 값을 저장하기 전에 공유 변수를 읽는 스레드가 존재할 것이다.

생각해보면 이는 가시성 문제와는 별개의 문제라는 것을 알 수 있다.

<br>

💫 따라서, 한 쓰레드만 쓰기 권한이 있고 그 외 쓰레드는 읽기 권한만 있는 경우에는 데이터의 일관성은 지켜질 수 있으나 2개 이상의 쓰레드가 쓰기 권한이 있다면 동기화가 필요하다.

<br>



## Atomic 과 CAS 알고리즘



멀티 쓰레드 환경에서 원자성을 보장하기위해 Atomic 변수라는 것이 존재한다.

> 📌 원자성 : 어떤 작업이 프로그램 안에서 가장 작은 단위라서 더 이상 다른 작업으로 나누어지지 않는 것

그 중에 하나인 AtomicInteger를 살펴보면

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230410213705254.png" alt="image-20230410213705254" style="zoom:80%;" />
</p>

volatile 변수를 value로 갖는 것을 볼 수 있다.

그리고 `compareAndSet` 메서드를 살펴보면

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230410213810524.png" alt="image-20230410213810524"  />
</p>

기댓값 `expectedValue`와 `newValue` 가 있는데,

① 인자로 기존값과 변경할 값을 전달한다.

② 변경할 값을 메모리에 전달하기 전, 메모리에 있는 값과 연산하기 전 기존값을 비교해서 일치한지 확인한다.

> 만약 다른 스레드가 변경을 한 뒤라면, 값이 일치하지 않을 것이다.

③ 만약 일치하면 값을 반영하고 다르다면 후처리를 한다.(후처리는 개발자가 결정)

위와 같은 메커니즘을 거치고, 이 메커니즘을 CAS(Compare And Swap 혹은 Compare And Set)이라고 한다.

<br>

💫 synchronized와 가장 큰 차이점은 Atomic 변수는 non-blocking 방식으로 처리할 수 있어 자원 낭비를 막을 수 있다.

{: .highlight-title }
> 참고한 블로그
>
> 1. [https://steady-coding.tistory.com/568](https://steady-coding.tistory.com/568)










