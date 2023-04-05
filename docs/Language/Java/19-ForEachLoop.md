---
layout: post
title: "· JAVA for each 문의 특징"
nav_order: 19
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/ForEachLoop
---

# JAVA for each 문의 특징
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>


{: .important-title}
> for-each문 핵심!
> 
> ***for-each***문은 ***Iterable 인터페이스를 상속하는 자료구조***에만 사용할 수 있다.
> 
> 인덱스를 꼭 사용해야하는 로직이 아니라면 증감식을 사용하는 for문보다 for-each문을 사용하는 것이 성능상 유리하다.


<br>

## for each 문이란?

<br>

```java
String[] numbers = {"one", "two", "three"};

// 일반 for loop 문
for(int i=0; i<numbers.length; i++) {
    System.out.println(numbers[i]);
}

// for each 문
for(String number: numbers) {
    System.out.println(number);
}
```



***for each문은 카운터 변수와 증감식을 사용하지 않는 loop 문***이다.

인덱스를 명시할 필요 없이 자료구조의 사이즈만큼 반복되기 때문에 코드가 간결해진다.

***for-each***문은 ***Iterable 인터페이스를 상속하는 자료구조***에만 사용할 수 있다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230405202426880.png" alt="image-20230405202426880"  />
</p>

실제로, Collection 인터페이스를 보면 Iterable을 상속하고 있다.

따라서, 우리는 ArrayList나 LinkedList와 같은 자료구조에 for-each 문을 사용할 수 있는 것이다.

<br>

```java
public interface Iterable<T> {

    Iterator<T> iterator();

    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }

    default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
    }
}
```

더 나아가서 ***Interable*** 인터페이스를 확인해보면, ***Interator*** 인터페이스를 반환하는 ***iterator()*** 메서드가 있다.

<br>

```java
public interface Iterator<E> {

    boolean hasNext();

    E next();

    default void remove() {
        throw new UnsupportedOperationException("remove");
    }

    default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }
}
```

그리고 ***Iterator*** 인터페이스에는 ***hasNext()*** 메서드와 ***next()*** 메서드가 있고, 이를 사용해서 for-each문을 사용하는 것이다.

<br>

```java
String[] numbers = {"one", "two", "three"};

// Iterable에 있는 iterator() 메서드로 Iterator를 생성한다.
Iterator<String> iterator = list.iterator();
 
// Iterator의 hasNext()로 다음 원소가 있는지 확인한다. 다음 원소가 없을때까지 loop문을 반복한다.
while (iterator.hasNext()) {
    // next() 메서드로 요소들을 순회한다.
	System.out.println(iterator.next());
}
```

for-each문의 동작 원리는 위와 같다.

<br>



## for-each문의 장점

<br>

```java
public class Test {

    public void printAll(List<Integer> list) {
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }
    }

    public void printAll2(List<Integer> list) {
        for (Integer integer : list) {
            System.out.println(integer);
        }
    }
}

```

위와 같이 `List<Integer> list` 를 파라미터로 받아 리스트 내의 원소를 출력하는 메서드가 있다고 생각해보자.

두 메서드가 하는 역할이 같지만, 어떤 것을 사용하는 것이 좋을까?

답은 for-each문을 사용한 두번째 메서드이다.

<br>

왜냐하면, 우리는 `List<Integer> list = new ArrayList<>();` 혹은  `List<Integer> list = new LinkedList<>();`

객체를 생성할 수 있고, 위 메서드를 사용할 수 있을것이다.

<br>

ArrayList 자료구조의 경우, 첫번째 메서드 처럼 인덱스를 사용하는 for 문을 사용할 때 해당 인덱스로 바로 접근할 수 있어 두 메서드의 성능차이가 크게 없지만

LinkedList 자료구조의 경우, 인덱스를 사용해서 for문을 수행하게 되면 ***매번 첫 번째 Head에서 해당 인덱스까지 이동해야하기 때문에***

해당 메서드의 경우 ***O(N^2) 시간 복잡도를 갖게 된다.***

<br>

따라서, 성능과 범용성을 생각해서 Collection 자료구조를 다룰 때, 인덱스가 꼭 필요한 상황이 아니라면 for-each문을 사용하는 것이 좋다.





{: .highlight-title }
> 참고한 블로그
>
> 1. [https://sorjfkrh5078.tistory.com/98](https://sorjfkrh5078.tistory.com/98)









