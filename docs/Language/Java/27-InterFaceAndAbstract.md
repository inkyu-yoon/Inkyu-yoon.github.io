---
layout: post
title: "·  JAVA Interface와 Abstract Class에 대한 고찰"
nav_order: 27
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/InterFaceAndAbstract
---

#  JAVA Interface와 Abstract Class에 대한 고찰
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Interface와 Abstract Class의 공통점과 차이점

### 📌 공통점

1. ***구현부가 작성되어 있지 않은 추상 메서드를 구현하도록 강제***하는 역할을 한다.
2. 추상메서드를 갖고 있기 때문에 ***인스턴스화 시킬 수 없다.***

### 📌 차이점

- ***Interface***의 경우 자바 8 이후 추가된 default · static 메서드를 제외한 메서드들은 ***구현부가 작성되어있지 않아야 한다.***
- ***Abstract Class***의 경우 구현부가 작성되어있지 않은 ***abstract method(추상메서드)가 1개 이상 존재***하면 된다.
- **Interface는 다중 구현(implements)이 가능**하지만, **Abstract Class는 다중 상속(extends)이 불가능**하다.


<br>

❓ 그렇다면 언제 어떤 것을 사용하는 것이 좋을까?

<br>


추상 클래스는 그 추상 클래스를 상속받아서 ***추상 클래스 내에 구현된 기능을 재사용하고 확장***시키는데 있다.

반면, 인터페이스는 추상 메서드를 구현하도록 강제하여 ***인터페이스를 구현한 클래스들에 대해서 같은 동작을 할 것이라는 보장***을 할 수 있다.

<br>


즉, 내가 생각했을 때는 클래스들 간에 공통된 부분이 많아서 코드의 중복을 줄이고 싶다면 추상 클래스를 사용하고

클래스들 간에 동일한 결과를 반환하는 기능을 강제로 구현하게 하고 싶을때는 인터페이스를 사용하면 될 것 같다.

<br>


따라서 추상 클래스와 인터페이스를 같이 상속받는 경우는,

**추상 클래스**를 상속받은 이유는 성격이 비슷한 클래스들 간 공통되는 부분이 있어 **코드의 중복 제거와 메서드 재사용성을 향상**시키기 위해 부모클래스를 정의한 뒤 상속받아 사용하는 것이고

**인터페이스**를 상속 받은 이유는 이 클래스에게 **특정 기능을 강제로 구현**하도록 하여 코드 유연성을 확보하기 위함일 것이다.


<br>


## 추상 클래스와 인터페이스 장단점 정리



### 추상 클래스의 장점

- 관련성이 높은 클래스 간 코드를 공유할 수 있다.
- 서로 관련이 깊은 클래스들 간에는 동일한 성격의 변수나 메서드들이 존재하게 되는데, 이를 추상 클래스에 정의해두고 상속받도록 구조를 변경하면 코드의 중복을 줄일 수 있다.

### 추상 클래스의 단점

- 다중 상속을 지원하지 않기 때문에 기능 구현을 강제하는데 한계가 있다.



### 인터페이스의 장점

- 서로 관계가 없는 클래스에게 공통 기능을 구현하도록 함으로서 관계를 맺어줄 수 있다.
- 인터페이스를 매개변수로 받아 인터페이스가 가진 메서드들로 코드를 작성하면, 인터페이스를 구현한 클래스들은 같은 동작을 하기 때문에 유연성 있는 코드가 된다.

### 인터페이스의 단점

- 꼭 필요하지 않은 메서드가 포함되어있어도 구현(implements) 하는 클래스들은 인터페이스가 가진 모든 메서드의 구현부를 완성해야한다.


<br>


## 추상 클래스와 인터페이스

### 추상 클래스 (Abstract Class)

```java
abstract class Sample{
    
    abstract void run();
  
    public void start(){ System.out.println("시작!");}
}
```

추상 클래스는 위와 같이 구현부가 없는 메서드를 갖고 있고, 구현부가 존재하지 않는 메서드는 ***abstract*** 키워드로 추상 메서드임을 명시해주어야 한다.



```java
abstract class Sample{
    
    void run(){};
  
    public void start(){ System.out.println("시작!");}
}
```

위와 같이 추상 메서드에 ***abstract*** 키워드를 제외하고 구현부를 비워놔도 되지만,

***abstract*** 키워드로 구현을 명시하면 상속받는 자손 클래스에서 꼭 구현이 필요하다는 것을 알려주는 것이 좋다.

<br>

```java
public class Test extends Sample{
    @Override
    void run() { System.out.println("시작!");}
}
```

추상 클래스를 상속받은 클래스는 추상 메서드를 무조건 구현해야하고, 그 외 추상 메서드가 아닌 메서드는 오버라이딩을 해도 되고 안해도 된다.



<br>

### 인터페이스 (Interface)

```java
interface Sample{
    public static final String value = "value";
    
    void run();

    static void start() { System.out.println("static 메서드!");}
    
    default void init() { System.out.println("default 메서드!");}
}
```

인터페이스는 위와 같이 구현부가 없는 추상메서드, static 메서드, default 메서드를 갖는다.

그리고 변수는 무조건 ***public static final*** 이어야 하기 때문에, 이는 제어자는 생략해도 자동적으로 추가된다.

<br>

```java
public class Test implements Sample{

    @Override
    public void run() { System.out.println("시작!");}

    @Override
    public void init() { System.out.println("오버라이딩!");}
}
```

인터페이스도 마찬가지로 구현하는 클래스에서 추상메서드를 무조건 구현해야한다.

static 메서드는 오버라이딩할 수 없지만, default 메서드는 구현하는 클래스에서 그냥 사용할 수도 있고 오버라이딩해서 사용할 수도 있다.



## 여러가지 경우 테스트

### 다중 상속 가능?  ❌

```java
public class Test extends Sample,Sample2 {

    @Override
    void run() { System.out.println("시작!");}

    @Override
    void run2() { System.out.println("시작2!");}
}

abstract class Sample{
    abstract void run();
}

abstract class Sample2{
   abstract void run2();
}
```

추상 클래스를 여러개 상속받아서 구현이 필요한 추상 메서드들을

다 구현해주기만 하면 될 것 같지만 컴파일 에러를 일으킨다.



지금처럼 추상 메서드만 존재하면 문제가 발생하지 않지만, 일반 메서드가 존재한다고 생각해보면 이해가 잘 될 것이다.

<br>

```java
abstract class Sample{
    abstract void run();
    
    void hello(){ System.out.println("안녕"); }
}
abstract class Sample2{
   abstract void run2();
    
   void hello(){ System.out.println("반가워");}
}
```

위와 같이, 일반 메서드 이름이 같은 클래스를 동시에 상속받은 클래스는

어떤 부모 클래스의 `hello()`를 동작시켜야 할 지 결정할 수 없기 때문이다.



### 상속의 상속 가능? ⭕

```java
public class Test extends Sample2 {

    @Override
    void run() { System.out.println("시작!");}

    @Override
    void run2() { System.out.println("시작2!");}
}

abstract class Sample{
    abstract void run();
}

abstract class Sample2 extends Sample{
   abstract void run2();
}
```

상속의 상속은 가능하다.

위 상황 처럼 `Test` 클래스에서 다 구현해주어도 되지만.

<br>

```java
public class Test extends Sample2 {
    @Override
    void run2() { System.out.println("시작2!");}
}

abstract class Sample{
    abstract void run();
}

abstract class Sample2 extends Sample{
    abstract void run2();
    
    @Override
    void run() { System.out.println("시작!");}
}
```

이런 식으로 `Sample2`에서 `Sample` 의 추상 메서드를 구현하는 것도 가능하다.

그러면 `Test` 클래스에서는 메서드 한개만 구현부를 작성하면 된다.

<br>

### 다중 인터페이스 구현 가능? ⭕

인터페이스는 다중으로 구현할 수 있다.

```java
public class Test implements Sample,Sample2{
    @Override
    public void run() { System.out.println("시작!");}
    @Override
    public void run2() { System.out.println("시작2!");}
}

interface Sample{
    void run();
}

interface Sample2{
    void run2();
}
```

구현하려는 인터페이스에 정의된 추상 메서드들을 다 구현해주기만 하면 된다.

<br>

```java
public class Test implements Sample,Sample2{

    //💡 컴파일 에러 발생!
    @Override
    public void run() { System.out.println("시작!"); }
}

interface Sample{
    void run();
}

interface Sample2{
    int run();
}
```

만약, 위와같이 Sample과 Sample2의 추상메서드의 이름은 같지만, 메서드의 반환 타입 혹은 파라미터 형식이 다른 경우에는 컴파일 에러를 일으킨다.



### 상속받으면서 인터페이스 구현 가능? ⭕

```java
package test;

public class Test extends Sample3 implements Sample, Sample2 {

    @Override
    public void run() { System.out.println("시작!");}

    @Override
    public void run2() {System.out.println("시작2!");}

    @Override
    public void run3() {System.out.println("시작3!");}
}

interface Sample{
    void run();
}
interface Sample2{
    void run2();
}

abstract class Sample3{
   abstract void run3();
}
```

인터페이스 2개와 추상 클래스 하나를 상속받는것도 가능하다.

단 구현이 필요한 메서드들을 다 구현해주기만 하면 된다.


<br>

{: .highlight-title }
> 참고한 글
>
> 1. 자바의 정석










