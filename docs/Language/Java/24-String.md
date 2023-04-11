---
layout: post
title: "·  JAVA String 클래스의 특징"
nav_order: 24
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/String
---

#  JAVA String 클래스의 특징
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



<br>



## String Constants Pool

String은 일반적인 Heap에 저장할 수 있고 또한 별도의 String Constants Pool이라는 공간에 저장될 수 있다.

```java
String text = "value";
String text2 = new String("value");
```

String 객체는 위와 같이 2가지 방법으로 생성할 수 있다.

String literal을 사용하여 생성한 객체는 String Constants Pool이라는 공간에 저장되고

생성자로 생성한 String은 Heap 영역에 저장된다.

<br>

```java
text == text2 -> false
text.equals(text2) -> true
```

따라서, literal로 생성한 text와 text2는 "value"라는 같은 값을 갖지만, 참조하고 있는 위치는 달라서 위와 같은 결과가 나올 것이다.

<br>

```java
String text = "value";
String text2 = new String("value");
String intern = text2.intern();

System.out.println(text == intern); -> true
```

String 클래스에는 intern() 이라는 메서드가 있는데, intern() 메서드는 생성된 String 객체를 String Pool에 추가하고 객체의 참조값을  반환한다.

이미 같은 값이 Pool에 존재한다면, 그 값의 참조값을 반환한다.

따라서 위 코드는 true를 반환할 것이다.

<br>

💫 결론은, literal로써 생성한 String 객체는 String Constants Pool에 같은 값이 이미 있는지 확인하고 참조값을 반환하므로

값이 같다면, 항상 같은 참조값을 갖는다. 즉 Immutable(불변)한 성질이 있다.

<br>

❓ 왜 이러한 성질을 갖도록 했을까?

<br>

1. String은 많이 사용되는 데이터 타입일것이라 예측했기 때문에, 같은 값을 갖는 경우에 참조값을 공유하지 않는다면
   매번 새로운 객체를 생성해야하고 메모리를 많이 차지하게 될 것이다.
2. String은 JVM의 클래스 로딩 메커니즘, 그리고 호스트 및 포트, 디렉터리 경로, DB 연결에 필요한 URL 등에 자주 사용되기 때문에 보안상 불변하지 않도록 했다.
3. 그 외, 멀티 쓰레딩 환경에서 스레드 간 String을 공유하는 과정이 쉬워지고 HashMap 및 Hashtable과 같은 해시 기반 컬렉션에서 키로 사용되어 좋은 성능을 가져온다.



<br>

{: .highlight-title }
> 참고한 블로그
>
> 1. [https://wildeveloperetrain.tistory.com/34](https://wildeveloperetrain.tistory.com/34)
> 1. [https://www.java67.com/2014/01/why-string-class-has-made-immutable-or-final-java.html](https://www.java67.com/2014/01/why-string-class-has-made-immutable-or-final-java.html)










