---
layout: post
title: "·  Java Equals()와 == 의 차이"
nav_order: 30
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/EqualsOverride
---

# Java Equals()와 == 의 차이
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



`==` 를 이용한 비교는 원시타입의 경우 해당 변수의 값 자체를 비교해서 사용할 수 있지만,

객체의 경우에는 참조하는 주소값을 비교하기 때문에 **Object의 equals() 메서드를 오버라이딩해서 사용해야한다.**

<br>

```java
public boolean equals(Object obj) {
        return (this == obj);
    }
```

기본적으로 Object에 정의되어있는 equals() 메서드는 `==` 를 사용해서 주소값을 비교하는 로직으로 정의되어있으므로

객체의 값을 비교하는 로직으로 동작하게끔 오버라이딩하는것은 필수적이다.

<br>

## Equals() 메서드 오버라이딩

<br>

❓ 어떤 로직으로 equals() 메서드를 오버라이딩하면 좋을까?

### 1. 먼저, **객체의 주소값이 같다**는 의미는 같은 데이터를 가리키고 있다는 의미이므로 같은 객체로 판단할 수 있다.

```java
public class Example {
    private String value;

    public Example(String value) {
        this.value = value;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj == this) {
            return true;
        }
    }
}
```

<br>

### 2. 파라미터로 들어온 obj가 **Example의 인스턴스인지 확인**한다. (Example 혹은 Example을 상속받은 클래스로 생성된 객체인지)

Example의 인스턴스가 아니라면 같다고 볼 수 없으므로 false를 반환한다.

```java
public class Example {
    private String value;

    public Example(String value) {
        this.value = value;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj == this) {
            return true;
        }
        
        if (!(obj instanceof Example)) {
            return false;
        }
    }
}
```

<br> 

### 3. 인스턴스임이 확인되면, **형 변환 후, 값을 비교한다.** 같은 값이라면 true를 반환하고 다른 값이라면 false를 반환한다.

```java
public class Example {
    private String value;

    public Example(String value) {
        this.value = value;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj == this) {
            return true;
        }
        
        if (!(obj instanceof Example)) {
            return false;
        }
        
        Example other = (Example) obj;
        return this.value == other.value;
    }
}
```

위 예시는 최종 결과를 `==` 연산자로 객체 내의 필드값을 비교했지만,

<br>

💡 객체안에 **새로 정의한 다른 객체가 있는 경우**는 `return Objects.equals(value, other.value);` 를 사용해야한다.

이미 설명했듯이, `==`로 주소값만 비교하면 안되기 때문이다.

```java
public static boolean equals(Object a, Object b) {
	return (a == b) || (a != null && a.equals(b));
}
```

위와 같이 null 체크도 해주고, 필드 객체에서 정의했을 equals()를 호출해주기 때문에 올바른 비교를 할 수 있다.


<br>

### 4. hashCode 메소드도 함께 오버라이딩하여 객체의 동등성 비교를 위한 해시코드를 반환해야 한다.

지금까지 작성한 코드로도 완벽해보이지만, 문제가 남아있다.

HashSet<> 과 같은 HashCode를 사용하는 자료구조를 사용할때 문제가 발생한다.

```java
public class Test {
    public static void main(String[] args) {
        Example example = new Example("test");
        Example example2 = new Example("test");
        System.out.println(example.equals(example2));

        HashSet<Example> set = new HashSet<>();
        set.add(example);
        set.add(example2);
        System.out.println(set.size());
    }
}
```

HashCode는 기본적으로 객체의 메모리 주소를 기반으로 생성하기 때문에

위 코드는, 두 객체가 다른 HashCode를 갖으므로 HashSet size가 2개인 것으로 나온다.

<br>

따라서, 객체 내의 내용이 완전 동일한 경우에는 같은 객체인것으로 만들기 위해서는 

**객체가 갖는 값을 기준으로 HashCode를 만들어 반환화도록 오버라이딩 해야한다.**

<br>

```java
class Example {
    private String value;
    public Example(){}
    public Example(String value) {
        this.value = value;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj == this) {
            return true;
        }

        if (!(obj instanceof Example)) {
            return false;
        }
        Example other = (Example) obj;

        return Objects.equals(value, other.value);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(value);
    }
}
```

이제는, `equals()` 메서드로 객체 내의 값이 완전히 동일하다면 같은 객체로 비교할 수 있으며, Hash 기반 자료구조도 정상적으로 사용할 수 있게 된다.




<br>

{: .highlight-title }
> 참고한 글
>
> 1. [https://inpa.tistory.com/entry/JAVA-%E2%98%95-equals-hashCode-%EB%A9%94%EC%84%9C%EB%93%9C-%EA%B0%9C%EB%85%90-%ED%99%9C%EC%9A%A9-%ED%8C%8C%ED%97%A4%EC%B9%98%EA%B8%B0](https://inpa.tistory.com/entry/JAVA-%E2%98%95-equals-hashCode-%EB%A9%94%EC%84%9C%EB%93%9C-%EA%B0%9C%EB%85%90-%ED%99%9C%EC%9A%A9-%ED%8C%8C%ED%97%A4%EC%B9%98%EA%B8%B0)