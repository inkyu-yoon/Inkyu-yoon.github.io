---
layout: post
title: "· JAVA Stream API 특징"
nav_order: 18
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/AboutStream
---

# JAVA Stream API 특징
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>


{: .important-title}
> Stream API 핵심!
> 
>
> Java8에 추가된 Stream API는 다량의 데이터 처리나 손쉬운 병렬 처리를 위해 만들어졌다.
> 
> Stream API는 일반적으로 데이터를 담고 있는 Collection의 개념이 아니라 어떤 시퀀스 요소들의 순차 및 병렬 집계 작업을 지원해주는 API이다.
> 
> 특징은 다음과 같다.
> 
> - 스트림이 처리하는 데이터소스는 변경되지 않는다.(원본 데이터를 훼손하지 않는다.)
> - 스트림으로 처리하는 데이터는 오직 한 번만 처리된다.
> - 최종 연산이 수행되기 전까지는 중간 연산이 수행되지 않는다.
> - 손쉬운 병렬 처리를 지원한다.


<br>


## Stream 이란!

많은 수의 데이터를 다룰 때, 컬렉션이나 배열에 데이터를 담고

원하는 결과를 얻기위해 for문과 Iterator를 이용해서 코드를 작성해왔다.

이런 방식으로 작성된 코드는 너무 길고 알아보기 어렵다. 그리고 재사용성도 떨어진다.

```
배열을 정렬할 때는, Arrays.sort()

List를 정렬할 때는, Collections.sort()
```

또한, 데이터 소스마다 다른 방식으로 다뤄야 한다는 점도 있다.

<br>

```java
// String 배열이 있을 때, 정렬한 뒤 원소를 출력해야 할때
String [] strArr = {"aaa","ddd","ccc"}

// 1. 일반적인 방식
Arrays.sort(strArr);
for(String str : strArr){
	System.out.println(str)
}

// 2. 스트림 방식
Arrays.stream(strArr).sorted().forEach(System.out::println)
```

***스트림은 데이터 소스를 추상화하고, 추상화된 데이터를 다루는데 자주 사용되는 메서드들을 정의해놓았다.***

데이터 소스가 무엇이던 간에 스트림으로 만들어놓기만 하면, 같은 방식으로 다룰 수 있어 ***코드 재사용성이 높아지고, 코드 가독성도 높아진다.***

또한 스트림에서 정의한 메서드 대로 불필요한 인스턴스 생성 과정을 생략할 수 있어, ***메모리 관점에서 효율적인 문법***이다.

<br>


## 스트림은 데이터 소스를 변경하지 않는다.

스트림은 데이터 소스를 변경하지 않는다.

단적인 예로,

```
Arrays.stream(strArr).sorted().forEach(System.out::println)
```

위 메서드를 실행한다고 해서, strArr 배열에 변화가 생기지 않는 것과 같다.

<br>


```
List<String> collect = Arrays.stream(strArr).sorted().collect(Collectors.toList());
```

스트림으로 변경을 시킨 데이터가 필요하다면, 위와 같이 ***.collect(Collectors.toList())*** 와 같은 방법으로 반환할 수 있다.

<br>


## 스트림은 일회용이다.

```java
public class Test {
    public static void main(String[] args) {
        String[] strArr = {"aaa", "ddd", "ccc"};
        Stream<String> stream = Arrays.stream(strArr);
        stream.forEach(System.out::println);

        //2번은 사용할 수 없다.
        stream.forEach(System.out::println);
    }
}
```

위와 같이 첫번째 ***forEach()*** 는 동작을 하지만,

두번째부터는 이미 사용했던 stream을 사용할 수 없다.

<br>


## 스트림은 작업을 내부 반복으로 처리한다.

반복문을 메서드 내부에 숨길 수 있어 간결하게 처리할 수 있는 것이다.

```java
EX) stream().forEach()

void forEach(Consumer<? super T> action){
    Objects.requireNonNull(action);

    for(T t : src){
        action.accpet(T)
    }
}
```

수행할 작업을 함수형 인터페이스의 형태로 받기 때문에 동작을 매개변수로 받아 연속적으로 처리할 수 있다.

<br>


## 지연 연산



```java
public class Test {
    public static void main(String[] args) {
        Integer[] arr = {1, 2, 3};

        Arrays.stream(arr)
                .peek((i)->System.out.println(i+"실행"))
                .forEach((i) -> System.out.println(i+"완료"));
    }
}
```

위 코드의 출력 결과는 어떻게 될까?

<br>


```java
1실행
2실행
3실행
1완료
2완료
3완료
```

중간 연산마다 모든 요소를 순회해서 연산을 마친후 다음 연산을 넘기는 방식인

위와 같이 실행될 것 같지만

```java
1실행
1완료
2실행
2완료
3실행
3완료
```

위 결과 처럼, 각 요소를 최종연산까지 진행한다.

<br>


어떤 것이 효율적일지 생각해보자

```java
for(Integer i : arr){
	print(i)
}

for(Intger i : arr){
	System.out.println(i+"완료")
}

//출력 결과
1실행
2실행
3실행
1완료
2완료
3완료

for(Integer i : arr){
	print(i)
	System.out.println(i+"완료")
}

//출력 결과
1실행
1완료
2실행
2완료
3실행
3완료
```

for문으로 풀어서 생각해보면, 첫번째 케이스의 경우 for문을 2번 돌지만, 두번째 케이스의 경우 for문을 1번만 돌아도 된다.

<br>


```java
public class Test {
    public static void main(String[] args) {
        Integer[] arr = {3, 2, 1};

        Arrays.stream(arr)
                .peek((i)->System.out.println(i+"실행"))
                .sorted()
                .forEach((i) -> System.out.println(i+"완료"));
    }
}

//출력결과
3실행
2실행
1실행
//여기서 sorted작업이 진행되어야 하므로 for문이 분리됨
1완료
2완료
3완료
```

> 📌 ***stateful한 중간 연산***
>
> - stateful (이전 상태를 참조해야하는 연산) : distinct, sorted…
> - stateless (이전 상태를 참조하지 않는 연산) : filter, map, limit, skip…
>
> sorted와 같은 stateful한 연산의 경우 모든 데이터가 주어져야 연산 결과가 나오지만, filter와 같은 stateless한 연산의 경우, 한 요소마다 결과를 넘길 수 있다.

<br>



위와 같은 원리로, 중간 연산의 형태에 따라 연산 순서가 정해지고, forEach()와 같은 최종 연산을 만나게 되면 중간 연산이 실행된다.

```java
Arrays.stream(arr)
                .peek((i)->System.out.println(i+"실행"))
                .sorted();
```

위와 같은 코드는 최종 연산 메서드가 없으므로 실행되지 않는다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230404145814820.png" alt="image-20230404145814820" style="zoom: 50%;" />
</p>

<br>

## 병렬 스트림

스트림으로 데이터를 다루면 병렬처리가 쉽다.

스트림에 ***parallel()*** 메서드를 추가하면 병렬로 연산을 수행한다.

<br>


```
// 일반적인 처리 (squential 생략 가능)
Arrays.stream(arr).sequential().sorted()
                .toArray();

// 병렬 처리 명시적 지시
Arrays.stream(arr).parallel().sorted()
                .toArray();
```

병렬처리의 경우 작업을 나누고 다시 합치는 시간이 필요하므로 **항상 더 빠른 결과를 얻게 해주는 것은 아니다.**

스트림 소스인 Collections이 split하기 **쉬운 자료구조**이어야 하며, 연산이 **stateful 하지 않아야 한다.**

> 📌 ***LinkedList vs ArrayList***
>
> LikedList는 next()를 통해 값을 일일이 참조해야하는데, ArryList는 index를 통해 List 값에 접근할 수 있어 작업을 split()하는데 비용이 적게 든다.
>
> LikedList 자료구조를 다룰땐,  for-loop 를 사용하는 것이 빠르다.

따라서, 테스트 해보고 결정하는 것이 좋다.

<br>

{: .highlight-title }
> 참고한 블로그
>
> 1. [https://bugoverdose.github.io/development/stream-operations/](https://bugoverdose.github.io/development/stream-operations/)
> 2. [https://velog.io/@lecharl/JAVA409](https://velog.io/@lecharl/JAVA409)









