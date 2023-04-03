---
layout: post
title: "· 람다식과 함수 인터페이스"
nav_order: 17
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/AboutLambda
---

# 람다식과 함수 인터페이스
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>


{: .important-title}
> 람다식과 함수 인터페이스 내용 핵심!
> 
> 함수를 선언할 때, 괄호와 화살표를 이용해 간단하게 선언할 수 있어 불필요한 코드를 줄일 수 있고 가독성을 높일 수 있어 만들어진 기능이다.
>
> 람다식은 ***함수형 인터페이스***의 인스턴스를 생성하여 함수를 변수처럼 다룰 수 있다.
> 
> 보통 Stream 연산들은 매개변수로 함수형 인터페이스를 받도록 되어있기 때문에 람다식을 많이 사용하게 된다.
> 
> 
> 
> ### 람다식의 장점
> 
> 1. 코드를 간결하게 만들 수 있고 가독성이 높아진다.
> 2. 함수를 만드는 과정이 없어 생산성이 높아진다.
>
> ### 람다식의 단점
>
> 1. 람다식은 재사용이 불가능하다.
> 2. 디버깅이 어렵다.
> 3. 비슷한 람다식이 너무 많이 반복 사용되면 코드가 지저분해질 수 있다.

<br>



## 람다식(Lambda Expression)이란?

<br>

자바 8버전에 추가된 기능중에 하나인 ***람다식(Lambda expression)*** 은 메서드를 하나의 식으로 표현한 것이다. 함수를 간략하면서도 명확한 식으로 표현할 수 있게 해준다.

메서드를 람다식으로 표현하면 메서드의 이름과 반환값이 없어지므로, 람다식을 '익명 함수'라고도 한다.

***람다식의 도입으로 자바는 객체지향언어인 동시에 함수형 언어가 되었다.***



***(매개변수) -> (식)*** 과 같은 구조를 이루고 있고, ***식의 연산 결과가 반환 값***이 된다.

```
 // 식의 연산 결과가 void 이므로 반환 값은 없음
 () -> System.out.println("람다식!!")
 
 // 식의 연산 결과가 int 이므로 반환 값은 a+b
 (a,b) -> a+b
```



구체적으로 말하자면, 람다식은 반환값으로 ***함수형 인터페이스***를 반환하고 있는데, 이는 뒤에 구체적으로 설명하겠다.


<br>


## 함수형 인터페이스

<br>

함수형 인터페이스란 ***1개의 추상 메서드***를 갖는 인터페이스를 말한다.

인터페이스 내에 여러개의 static · default 메서드가 있는 것은 상관없다.

<br>


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230403164855749.png" alt="image-20230403164855749" style="zoom:50%;" />
</p>

`@FunctionalInterface` 어노테이션을 붙여주면 추상 메서드가 하나인지 검사를 해주는데, 인터페이스 검증과 유지보수를 위해 붙여주는 것이 좋다.

> Java에서 제공하는 함수형 인터페이스로도 충분해서, 직접 함수형 인터페이스를 만들 일은 거의 없긴 하다.



그렇다면 함수형 인터페이스가 왜 필요할까?

람다식은 독자적으로 쓰일 수 없기 때문에, 참조변수를 통해서 호출할 수 있어야 한다.

람다식을 연결해주는 객체가 필요한데, 추상메서드를 가진 인터페이스를 구현받아 사용하는 방식이 기존 자바의 규칙을 어기지 않으면서 자연스러운 방식이기 때문이다.

***즉, 람다식을 다루기 위한 추상 메서드를 1개만 가진 인터페이스를 함수형 인터페이스 라고 부른다.***

함수형 인터페이스의 원리에 대해서 살펴보자.

<br>

### 람다식이 추가되기 전과 후의 차이

<br>

```java
public interface Sample {
    void print();
}

public class Excercise {
    public static void main(String[] args) {
        // 익명 클래스 생성 후, 추상 메서드를 구현하여 사용
        Sample sample = new Sample() {
            @Override
            public void print() {
                System.out.println("안녕");
            }
        };
        sample.print();
        
        // 람다식을 사용하여 구현부를 작성하고 사용 
        // (단, 람다식의 반환값이 인터페이스의 메서드 반환 타입과 일치해야하고 인터페이스는 메서드가 하나만 있어야한다.)
        Sample sample2 = () -> System.out.println("안녕");
        sample2.print();
    }
}

```



람다식이 등장하기 이전에는 모든 메서드는 클래스에 포함되어야 하므로 클래스를 새로 만들고, 새로 만든 클래스에 메서드를 만든 뒤 객체를 생성해야만 메서드를 호출할 수 있었다.

***하지만 람다식은 람다식 자체만으로 메서드의 역할을 대신할 수 있다.***

단, 람다식의 반환값이 인터페이스의 메서드 반환 타입과 일치해야하고 인터페이스는 메서드가 하나만 있어야한다.

<br>

### 람다식이 객체(참조변수)에 연결될 수 있는 조건

<br>

```java
public interface Sample {
    // 추상메서드 1
    void print();
    
    // default 메서드
    default void defaultPrint(){
        System.out.println("default Print!!");
    }
    
    // static 메서드
    static void staticPrint2(){
        System.out.println("static Print!!");
    }
    
    // 추상메서드가 2개안 경우 에러 발생!!
    // void print2();
}

public class Excercise {
    public static void main(String[] args) {
        // 추상 메서드가 1개라면 정상 동작
        Sample sample2 = () -> System.out.println("안녕");
        sample2.print();
    }
}
```

위 경우에서 ***Sample*** interface에 추상 메서드가 1개라면 람다식으로 대체가 가능하지만,

추상 메서드가 2개면 람다식과 1:1로 연결될 수 없어 컴파일 에러가 발생한다.

<br>

### 람다식은 메서드 파라미터로도 활용 가능!

<br>

```
Sample sample = () -> System.out.println("안녕");
```

위와 같이 함수 인터페이스에 람다식을 할당했다.

이 원리를 생각하면, 파라미터로 넘기는 방법도 쉽게 떠올릴 수 있다.

<br>

람다식을 특정 메서드의 파라미터로 넘기는 방법은 다음과 같다.

```java
public interface Sample {
    void print();
}


public class Excercise {
    public static void main(String[] args) {
        lambdaParameterMethod(()-> System.out.println("람다식 넘겨주기!"));

        lambdaParameterMethod(returnLambda());
    }

    public static void lambdaParameterMethod(Sample sample) {
        sample.print();
    }

    public static Sample returnLambda(){
        return () -> System.out.println("람다식 반환!!");
    }
}

```

***lambdaParameterMethod()*** 메서드는 파라미터로 ***Sample***을 입력받는다.

따라서, void 반환을 하는 람다식은 모두 파라미터로 입력될 수 있을 것이다.

또한, ***returnLambda()*** 는 반환타입이 ***Sample***이므로, void 반환을 하는 람다식을 반환할 수 있을 것이다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230403163633093.png" alt="image-20230403163633093" style="zoom: 67%;" />
</p>

정리해보면 위와 같다.

<br>

## 자바가 제공하는 함수형 인터페이스

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230403222449061.png" alt="image-20230403222449061" style="zoom:80%;" />
</p>

void 반환, boolean 반환, 객체 반환 등등 다 웬만한 것들은 다 정의되어 있으므로, 사용하면 된다.

다만, 매개변수가 3개 이상인 것들은 따로 함수형 인터페이스를 정의해서 사용해야한다.

<br>

{: .highlight-title }
> 참고한 블로그
>
> 1. [https://mangkyu.tistory.com/113](https://mangkyu.tistory.com/113)









