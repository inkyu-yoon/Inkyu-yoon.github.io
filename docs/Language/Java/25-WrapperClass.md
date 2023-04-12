---
layout: post
title: "·  JAVA Wrapper Class 란?"
nav_order: 25
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/WrapperClass
---

#  Wrapper 클래스란?
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


자바에는 Primitive type(원시 타입) 자료형은 8개가 있다.

> byte, short, int, long, float, double, char, boolean

Primitive type(원시 타입) 자료형은 각 스레드의 stack 메모리 영역에 저장되어 관리된다.

반면에 객체와 같이 참조 타입 데이터는 Heap 영역에 생성되고, stack에는 Heap영역의 주소값을 통해 참조하는 방식으로 사용한다.

<br>

원시 타입은 각각 Wrapper Class(랩퍼 클래스) 를 갖고 있는데,

예시로, int의 경우 Integer가 있고 long의 경우 Long이 있다.

```
int num = 1;
Integer boxed = new Integer(num);
int unBoxed = boxed.intValue();
```

위와 같이 원시 타입 int인 num을 랩퍼 클래스로 변환할 수 있다.

원시타입에서 랩퍼클래스로 변환하는 과정을 Boxing(박싱), 랩퍼 클래스에서 원시 타입으로 변환하는 과정을 UnBoxing(언박싱) 이라고 한다.

<br>

```
Integer autoBoxed = 10;
int autoUnBoxed = autoBoxed;
```

위와 같이 원시 타입에 랩퍼 클래스를 대입하거나 그 반대 상황일 때, 형 변환이 자동으로 이루어지는데 이를 오토 박싱 · 오토 언박싱이라고 한다.

<br>

⚠️ 주의점!

```java
public static void main(String[] args) {
    Long sum = 0L;
    for (long i = 0; i < 1000000; i++) {
        sum += i;
    }
}
```

위와 같은 코드는 sum이 랩퍼 타입이고 반복문을 수행하면서 Long 객체가 100만개 생성되게 된다.

따라서, 꼭 필요한 상황인지 혹은 성능상 문제가 발생하지 않는지 확인하고 랩퍼 클래스를 사용해야한다.



<br>

{: .highlight-title }
> 참고한 블로그
>
> 1. [https://jaehoney.tistory.com/101](https://jaehoney.tistory.com/101)










