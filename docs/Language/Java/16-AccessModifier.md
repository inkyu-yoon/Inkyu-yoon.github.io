---
layout: post
title: "· 자바의 접근 제어자와 protected와 private는 왜 사용되는가?"
nav_order: 16
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/AccessModifier
---

# 자바의 접근 제어자와 protected와 private는 왜 사용되는가?
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---




{: .important-title}
> 핵심
> 
> 접근제어자는 private, default, protected, public 가 있으며,
>
> private 는 해당 클래스만 접근할 수 있고,
>
> default는 같은 패키지에 있는 경우에만 접근할 수 있고
>
> protected는 같은 패키지에 있거나 다른 패키지인 경우 상속받은 클래스만 접근할 수 있고
>
> public은 어디서든 접근할 수 있습니다.
>
>
> 제가 생각하기에는 protected 제어자의 경우 외부의 접근은 제한하지만 상속받는 객체에게 필드나 메서드를 사용 또는 확장하도록 강조하고 싶을 때 사용한다고 생각합니다.
> 
> private 제어자의 경우 외부에서 직접 접근할 수 없기 때문에 정보의 은닉화와 캡슐화를 위해 사용한다고 생각합니다.

<br>

자바는 접근제어자를 통해 변수나 메서드의 접근을 제어할 수 있다.

접근 제어자는 **private**· **default**· **protected**· **public**  4가지가 있다.

<br>

### private

<br>

**private** 접근 제어자를 사용하면 해당 클래스만 사용할 수 있다.

```java
public class Sample {
    private String secret;
}

// Sample과 같은 패키지
public class Sample2{
    void test(){
        Sample sample = new Sample();
        System.out.print(sample.secret) // 같은 패키지여도 다른 클래스에서 사용하려고 하면 에러 발생
    }
}
```

예를 들어 위와 같은 클래스가 있다고 생각해보자

변수 **secret** 과 메서드 **getSecret()** 이 접근 제어자 **private**가 명시되어있다.

이 변수와 메서드는, **Sample** 클래스 내에서만 사용할 수 있고

같은 패키지에 있는 다른 클래스에서 **Sample** 객체를 생성해도 접근할 수 없다.

외부에서 접근하고 싶다면 메서드를 정의해야 접근할 수 있기 때문에 유효하지 않은 데이터로 변경되지 않도록 할 수 있다.

<br>

### default

<br>

접근 제어자를 별도로 명시하지 않으면 **default** 접근 제어자가 된다.

```java
public class Sample {
    String secret;
    public Sample(){
        this.secret = "secret";
    }
}


// Sample과 같은 패키지
public class Sample2{
    void test(){
        Sample sample = new Sample();
        String value = sample.secret;
    }
}

// Sample과 다른 패키지
public class Sample3{
    void test(){
        Sample sample = new Sample();
        String value = sample.secret; //컴파일 에러 발생
    }
}
```

위와 같이, 같은 패키지 내에서만 접근할 수 있다.

<br>



### protected

<br>

**protected**는 상속 받은 클래스 혹은 같은 패키지에 있는 경우 접근할 수 있다.

```java
public class Sample {
    protected String secret;
    public Sample(){
        this.secret = "secret";
    }
}


// Sample과 같은 패키지에 있으면 접근 가능
public class Sample2{
    void test(){
        Sample sample = new Sample();
        String value = sample.secret;
    }
}

// Sample과 다른 패키지이지만, 상속받은 클래스라면 접근 가능
public class Sample3 extends Sample{
    void test(){
        String value = secret;
    }
}

// Sample과 다른 패키지는 접근 불가
public class Sample4{
    void test(){
        Sample sample = new Sample();
        String value = sample.secret; // 컴파일 에러 발생
    }
}
```

다른 패키지에 있는 클래스여도, 상속받은 클래스라면 **protected** 접근 제어자에 접근할 수 있다.

protected 제어자는 상속을 통해 확장될 것이 예상되고 외부 접근은 제한을 줘야 할 때 사용한다.

<br>

### public

<br>

**public** 접근 제어자는 어떤 위치에서나 접근할 수 있다.

```java
public class Sample {
    public String secret;
    public Sample(){
        this.secret = "secret";
    }
}


// Sample과 같은 패키지
public class Sample2{
    void test(){
        Sample sample = new Sample();
        String value = sample.secret;
    }
}

// Sample과 다른 패키지
public class Sample3{
    void test(){
        Sample sample = new Sample();
        String value = sample.secret;
    }
}
```

객체만 초기화 한다면, 위와 같이 다 사용할 수 있다.

<br>












