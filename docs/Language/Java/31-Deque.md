---
layout: post
title: "·  Java Vector·Stack 대신 Deque를 사용하자"
nav_order: 31
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/Deque
---

# Vector·Stack 대신 Deque를 사용하자
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## Vector와 Stack을 사용하지 않는 이유

<br>

Stack 자료구조의 대표적인 기능 `push()` 와 `pop()` 을 살펴보자.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230424200458493.png" alt="image-20230424200458493" style="zoom: 50%;" />
</p>

***Stack***의 `push()` 메서드는 ***Vector***의 `addElement()` 메서드를 사용하는데, `synchronized `메서드인 것을 볼 수 있고, `pop()` 메서드 역시 `synchronized `메서드이다.

`synchronized` 메서드는 한 번에 하나의 스레드만 해당 메서드를 실행할 수 있도록 해서 스레드 안정성을 보장할 수 있다.



<br>



💡그러나 ***이러한 동기화는 성능 저하를 초래할 수 있다.***

다수의 스레드가 동시에 접근하는 상황에서 시간적으로 비효율적이며, 성능 저하를 초래할 수 있다.

⚠️  심지어 `synchronized` 메서드는 멀티 스레드 환경에서 발생하는 모든 문제를 해결할 수도 없다. (ex. 가시성 문제)



<br>



💡 그렇다고 ***단일 스레드에서 사용하는 것도 비효율 적이다.***

왜냐하면, 여러 스레드가 공유 자원에 동시에 접근하지 않으므로, ***동기화가 필요하지 않기 때문이다.***

그러나 Vector와 Stack은 메소드 호출 시 마다 동기화를 사용하기 때문에, 다른 자료구조에 비해 더 많은 시간과 리소스를 소비할 수 있다.



<br>

❓그렇다면 어떤 자료구조를 사용하는 것이 좋을까?



<br>

## Deque 인터페이스를 사용하자

***Deque(Double Ended Queue)*** 는 양쪽 끝에서 요소의 삽입과 삭제가 가능한 자료구조이다.

즉, 스택과 큐의 기능을 모두 갖고 있다.

<br>

```java
Deque<String> deque = new ArrayDeque<>();

Deque<String> deque = new LinkedList<>();
```

위와 같이 ArrayDeque 혹은 LinkedList 구현 클래스를 사용해서 초기화하면 된다.

>  📌 ArrayDeque : 배열을 사용해 구현되어 있어, 메모리에 연속 할당되어있다. 따라서 캐시 효율성이 높고 인덱스를 사용하여 원소에 빠르게 접근할 수 있다.
>
> 📌 LinkedList : 각 요소에 대한 포인터를 갖고 있기 때문에 요소에 대한 삽입 · 삭제가 빠르지만, 요소에 접근할 때는 포인터를 따라가야 하므로 느리다.

<br>

```java
Deque<String> deque = Collections.synchronizedDeque(new ArrayDeque<String>());
```

Deque의 경우 멀티 스레드 환경에서 동시성 문제를 발생시킬 수 있으므로 위와 같이 초기화하면 동기화 처리된 Deque를 생성할 수 있다.

근데 이런식으로 생성한 Deque는 Stack과 Vector처럼 모든 메서드에 `synchronized` 처리가 되어있으므로, 이 역시 ***모든 동시성 문제를 해결하지 못한다.***

<br>

```java
ConcurrentLinkedDeque<String> deque = new ConcurrentLinkedDeque<>();

LinkedBlockingDeque<String> deque = new LinkedBlockingDeque<>();
```

`ConcurrentLinkedDeque`는 lock-free 알고리즘을 사용하여 스레드 안전성을 보장하며, `LinkedBlockingDeque`는 내부적으로 잠금(lock)을 사용하여 스레드 안전성을 보장한다.

> ### 📌 lock-free 알고리즘 : 락을 사용하지 않고 스레드 안정성을 보장하는 방법, 대표적으로 CAS(Compare And Sweep)가 있다.
>
> ① 인자로 기존값과 변경할 값을 전달한다.
>
> ② 변경할 값을 메모리에 전달하기 전, 메모리에 있는 값과 연산하기 전 기존값을 비교해서 일치한지 확인한다.
>
> > 만약 다른 스레드가 변경을 한 뒤라면, 값이 일치하지 않을 것이다.
>
> ③ 만약 일치하면 값을 반영하고 다르다면 후처리를 한다.(후처리는 개발자가 결정)



<br>

Deque는 스택과 큐의 기능을 모두 사용할 수 있는 자료구조이다.

따라서, 특정 메서드만 사용하면 Stack과 같은 기능으로 사용할 수 있다.

<br>

## Deque의 비슷한 메서드 차이점 살펴보기



### addXX() vs offerXX()

- addXX() : 앞쪽 혹은 뒷쪽에 엘리먼트를 삽입한다. **용량 제한이 있는 경우, 용량을 초과하면 예외 발생**
- offerXX() : 정상적으로 엘리먼트가 삽입된 경우 true가 리턴되며, **용량 제한에 걸리는 경우 false를 리턴한다.**

add는 void 메서드이며 용량 제한을 넘는 경우 예외 발생!

offer boolean 메서드이며 용량 제한을 넘는 경우 false 반환!

### removeXX() vs pollXX()

- removeXX() : 앞쪽 혹은 뒷쪽 엘리먼트를 제거한 후, 리턴한다. **덱이 비어있으면 예외 발생**
- pollXX() : 앞쪽 혹은 뒷쪽 엘리먼트를 제거한 후, 리턴한다. **덱이 비어있으면 null 반환**

remove는 덱이 빈 경우, 예외 발생!

poll은 덱이 빈 경우, null 반환!



<br>

{: .highlight-title }
> 참고한 글
>
> 1. [https://inpa.tistory.com/entry/JAVA-%E2%98%95-equals-hashCode-%EB%A9%94%EC%84%9C%EB%93%9C-%EA%B0%9C%EB%85%90-%ED%99%9C%EC%9A%A9-%ED%8C%8C%ED%97%A4%EC%B9%98%EA%B8%B0](https://inpa.tistory.com/entry/JAVA-%E2%98%95-equals-hashCode-%EB%A9%94%EC%84%9C%EB%93%9C-%EA%B0%9C%EB%85%90-%ED%99%9C%EC%9A%A9-%ED%8C%8C%ED%97%A4%EC%B9%98%EA%B8%B0)