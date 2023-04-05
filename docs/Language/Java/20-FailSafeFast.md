---
layout: post
title: "· JAVA Fail-Safe · Fail-Fast Iterator란?"
nav_order: 20
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/FailSafeFast
---

#  JAVA Fail-Safe · Fail-Fast Iterator란?
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>


{: .important-title}
> Fail-Safe Iterator vs Fail-Fast Iterator 핵심!
> 
> 자바에서는 ***Iterator를 사용한 for 문 순환 중*** 데이터가 변경되었을때
> 
> ***Fail-Fast systems은 에러를 발생시키며 작업을 중단***한다.
> 
> 반면 ***Fail-Safe systems은 장애 발생시 작업을 중단하지 않는다.***





## Fail-Safe Iterator와 Fail-Fast Iterator

<br>

Fail-Fast는 iterator를 이용한 순환중(즉, for-each문 사용시) 데이터가 변경되었을때(데이터를 추가하거나 삭제할 때) 즉시, ***ConcurrentModificationException***  에러를 발생시킨다.

같은 상황에서, Fail-Safe는 에러를 발생시키지 않는다.

<br>

💡 Fail-Safe Iterator는 실제 Collection의 복제본을 사용하기 때문이다.

따라서, Iterator가 실제 Collection 대신 복제본에서 작업하고 있기 때문에 에러를 발생시키지도 않고, Collection에 업데이트 된 데이터를 반환하지도 않는다.

ConcurrentHashMap, CopyOnWriteArrayList 등과 같은 java.util.concurrent 패키지의 콜렉션에있는 반복자는 본질적으로 Fail-Safe이다.
> 참고로 ***ConcurrentHashMap*** 의 경우 사본을 이용한 방식이 아닌 ***Lock***을 이용한 방식이라 ***weakly consistent iterator***라고 표현한다.)

<br>

## Fail-Fast 예시

<br>

```java
public class Test {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        for (Integer integer : list) {
            System.out.println(integer);
            list.add(3);
        }
    }
}

```

for-each문은 내부적으로 자료구조가 구현한 Iterator 인터페이스의 메서드를 사용한다.

따라서, 위와 같이 iterator를 이용해 순환중일 때, 데이터가 추가되면 ***ConcurrentModificationException*** 에러를 발생시킨다.



컬렉션은 요소가 추가되거나 삭제연산이 수행될 때 마다 증가하는 ***modCount***라는 변수를 가지고 있는데,

***iterator***의 ***next()*** 를 호출할때마다 ***modCount***의 값이 변했는지 확인하고, 다르면 에러를 발생시키도록 구현되어 있기 때문이다.

<br>

```java
public class Test {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
            list.add(3);
        }
    }
}
```

반면에 위와 같은 경우는, 구현한 iterator를 사용하지 않았기 때문에 ***ConcurrentModificationException*** 에러는 발생시키지 않지만,

list에 원소가 계속 추가되어 무한 루프를 돌게 된다.





## Fail-Safe 예시

<br>

```java
public class Test {
    public static void main(String[] args) {
        CopyOnWriteArrayList<Integer> list
                = new CopyOnWriteArrayList<Integer>(new Integer[] { 1, 3, 5, 8 });
        for (Integer integer : list) {
            System.out.println(integer);
            list.add(10);
        }
    }
}

```

Fail-Safe한 자료구조중 하나인 ***CopyOnWriteArrayList***를 사용해보면, for-each문에 데이터 추가가 되었지만 에러가 발생하지 않는것을 알 수 있다.

CopyOnWriteArrayList는 원본 collection을 카피한 후, 카피한 collection으로부터 iterator를 생성하여 사용하는 ***fail safe iterator***이다.

<br>

***ConcurrentHashMap***의 경우에는 추가/삭제 메서드에 synchronized 키워드를 사용하여 락을 잡은 후

다른 쓰레드에서 건드리지 못하도록 하기에 동시성으로부터 안전하고 복사된 컬렉션이 아닌 원본 컬렉션에 대해 순회를 한다.

따라서, ConcurrentHashMap는 사본을 이용한 방식이 아니므로 fail-safe 방식은 아니고 weakly consistent 방식이라고 한다.

<br>

## 정리

<br>

Fail-Fast 방식의 경우 다른 스레드가 접근 시 에러를 발생 시켜, 컬렉션 데이터의 안전성에 치중한 방식인 것 같고

Fail-Safe나 weakly consistent 방식의 경우 여러 쓰레드가 접근할 수 있기 때문에 작업 효율에 신경을 쓴 방식인 것 같다.


{: .highlight-title }
> 참고한 블로그
>
> 1. [https://simuing.tistory.com/entry/JAVA-Fail-Safe-Iterator-vs-Fail-Fast-Iterator](https://simuing.tistory.com/entry/JAVA-Fail-Safe-Iterator-vs-Fail-Fast-Iterator)
> 2. [https://june0122.tistory.com/5](https://june0122.tistory.com/5)









