---
layout: post
title: "·  Java Generic 이란?"
nav_order: 29
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/Generic
---

# Java Generic 이란?
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



어떤 값이 하나의 참조 자료형이 아닌 여러 참조 자료형을 사용할 수 있도록 프로그래밍 하는 것을 **'제네릭(Generic) 프로그래밍'** 이라고 한다.

자료형 별로 메서드를 만들면 코드의 효율성이 떨어지기 때문에, 여러 참조 자료형이 쓰일 수 있는 곳에 특정한 자료형을 지정하지 않고,

**클래스나 메서드를 정의한 후 사용하는 시점에 어떤 자료형을 사용할 것인지 지정**하는 방식이다.



## Generic의 장점

- 제네릭은 참조 자료형이 변환될 때 이에 대한 검증을 **컴파일러가 하므로 안정적**
- 클래스 **외부에서 타입을 지정**해주기 때문에 따로 **타입을 체크하고 변환해줄 필요가 없다.**
- 비슷한 기능을 지원하는 경우 **코드의 재사용성이 높아진다.**



### 장점을 예시로 알아보자

```java
public class Test {
    public static void main(String[] args) {
        Sample sample = new Sample();
        int i = sample.get(1);
        String text = sample.get("문자열");
    }
}

class Sample{
    String get(String input) {
        return input;
    }
    int get(int input) {
        return input;
    }
}
```

위와 같이 파라미터로 String과 int 타입 파라미터를 받는 메서드가 있다고 할때,

자료형에 맞게 메서드 2개를 오버로딩해서 구현해도 되지만,

<br>

```java
class Sample{
    Object get(Object input) {
        return input;
    }
}
```

가장 상위 객체인 Object를 이용해서, 위와 같이 메서드를 작성해도 될 것이다.

하지만, 위 방식은 단점이 있는데 우리가 원했던 기능은 String을 입력하면 String 문자열을 반환받았고, int를 입력하면 int를 반환받았다.

<br>

```java
public class Test {
    public static void main(String[] args) {
        Sample sample = new Sample();
        String text = (String)sample.get("text");
        int i = (int) sample.get(1);
    }
}
```

모든 클래스의 조상인 Object를 입력받아 Object를 반환하므로 범용성이 좋다고 할 수 있지만,

다시 원하는 타입으로 반환받기 위해서는 **Casting 작업이 수반된다.**

<br>

```java
public class Test {
    public static void main(String[] args) {
        List list = new ArrayList<>();
        list.add("문자열");
        String str = list.get(0); //💡list.get() 해서 나온 객체는 Object이므로 컴파일 에러 발생 
    }
}
```

우리가 리스트 컬렉션을 사용할 때도 위와 같은 이유로 제네릭 타입을 항상 명시해주는 이유이다.

💡 위 상황은 **List에 타입을 명시해주지 않을 경우** Object로 보관을 하기 때문에, 

반환할 때는 **다시 String 형변환을 해주어야** 에러가 발생하지 않는다.

`List<String> list = new ArrayList<>();` 로 list를 생성하면 **String 타입의 객체만 입력됨을 보장**해주기 때문에 **Casting(형 변환) 없이 String으로 반환받을 수 있던 것**이다.

 <br>

🌟 결론은 이 모든 단점을 해결해주는 방식이 **제네릭 방식**인 것이다.

```java
class Sample{
    <T> T get(T input) {
        return input;
    }
}
```

메서드 앞에 `<T>`(꼭 T가 아니어도 됨) 와 같이 <> 안에 타입 기호를 정의해서 제네릭이라는 표시를 해주고 메서드를 작성하면 된다.

이런식으로 제네릭 메서드를 작성하면, **T라는 타입이 들어와서 T라는 타입으로 반환됨을 명시**해주었기 때문에,

**형변환도 필요없고** 중복되는 메서드도 줄일 수 있다.

제네릭 메서드를 예시로 들었지만, 제네릭 클래스도 위와 같은 이유로 사용된다.

<br>

```java
class Sample <T> {
    T value;
    public Sample(T t){
        this.value = t;
    }
	T get(){
        return value;
    }
}
```

제네릭 클래스는 위와 같이 클래스 레벨에 `<타입 기호>`를 명시하고 사용하면 된다.

<br>

## 제네릭 사용 시 주의점

### 제네릭 클래스와 제네릭 메서드의 타입기호는 동일 타입이 아니다.

```java
class Sample <T> {
    T value;
    public Sample(T t){
        this.value = t;
    }
    
    <T> T getInput(T input) {
        return input;
    }
}
```

위와 같이 **제네릭 클래스와 제네릭 메서드가 함께 있는 클래스**가 있을 때,

제네릭 메서드에서 사용한 `T`와 클래스에서 사용한 `T`는 **동일 타입이 아니라는 것**을 주의해야한다.

<br>

```java
public class Test {
    public static void main(String[] args) {
        Sample<Integer> sample = new Sample(1);
        System.out.println(sample.getInput("안녕").getClass());
    }
}
```

Sample 객체를 생성할 때, 제네릭 타입을 Integer를 명시했지만 위 코드는 `getInput()` 파라미터로 문자열을 넣어도 정상 동작하고

getClass()를 했을 때, String 클래스가 나오는 것을 확인할 수 있다.

즉 독립적인 타입이라는 것을 알 수 있다.

<br>

```java
class Sample <T> {
    T value;
    public Sample(T t){
        this.value = t;
    }
    
    T getInput(T input) {
        return input;
    }
}
```

클래스 레벨에서 사용한 타입 기호를 메서드레벨에서도 적용시키고 싶다면 **메서드 앞에 별도의 제네릭 표시를 제외하고** 메서드를 작성해야한다.

<br>

### 제네릭 클래스는 다형성을 허용하지만, 명시한 타입 기호는 다형성이 허용되지 않는다.

```java
public class Test {
    public static void main(String[] args) {
        Dad<String> dad = new Son<>();
    }
}
class Son <T> extends Dad{
    T value;
}
class Dad<T>{
}
```

위와 같이 두 제네릭 클래스가 상속관계에 있을 때는, 다형성으로 제네릭 객체를 생성할 수 있지만

<br>

```java
public class Test {
    public static void main(String[] args) {
        Family<Dad> family = new Family<Son>(); //💡 에러 발생!
    }
}

class Family<T>{
    T value;
}

class Son extends Dad{
}
class Dad{
}
```

위와 같이 제네릭 타입은 다형성이 적용되지 않는다.

<> 안에 들어가는 제네릭 타입 기호는 무조건 동일해야하고,

따라서 `Sample<Dad> sample = new Sample<>();` 처럼 생략이 가능하다.

<br>

## 제네릭 타입 제한과 와일드 카드

```java
public class Test {
    public static void main(String[] args) {
        Family<Dog> family = new Family<>();
    }
}

class Family<T>{
    T value;
}
class Dog{
}
class Son extends Dad{
}
class Dad{
}
```

위 상황에서 제네릭 클래스인 Family에 Dog 클래스로 명시하지 못하도록 특정 타입을 **제한하는 방법은 extends를 사용**하면 된다.

`<T extends 클래스>` 로 제네릭을 명시하면 **클래스의 자손 타입만 대입될 수 있다.**

<br>

```java
public class Test {
    public static void main(String[] args) {
        Family<Dog> family = new Family<>(); //💡 Dog 클래스는 Dad를 상속받는 클래스가 아니다.
    }
}

class Family<T extends Dad>{
    T value;
}
class Dog{
}
class Son extends Dad{
}
class Dad{
}
```

위 코드는 `Dad` 클래스를 상속하는 클래스만 제한을 두었기 때문에 `Dog` 클래스를 대입하려고 하면 에러가 발생하게 된다.

⚠️ 만약 ***특정 인터페이스를 구현한 객체로 제한***하고 싶은 경우에도 ***implements***가 아닌 ***extends***를 사용하면 된다.

<br>

### 제네릭 타입 와일드 카드

```java
public class Test {
    public static void main(String[] args) {
        Ex.getName(new Family<Dad>());
        Ex.getName(new Family<Son>());
    }
}

class Ex{
    static void getName(Family<Dad> family) {
    }
    
    static void getName(Family<Son> family) { //💡 컴파일 에러 발생
    }
}
class Family<T extends Dad>{
    T value;
}

class Son extends Dad{
}
class Dad{
}
```

조금 억지스러울 수 있겠지만, 위와 같은 코드가 있다고 생각해보자.

Family 객체는 `Family<Dad> family = new Family<>();` 혹은 `Family<Son> family = new Family<>();` 로 생성될 수 있고

<br>

이를 매개변수로 받아 처리하는 메서드를 만든다고 생각해보면,

위와 같이 타입 기호안에 있는 객체는 다형성이 성립하지 않기 때문에 `getName()` 메서드를 2개 정의해주어야 한다.

<br>

하지만, 컴파일에러가 발생한다.

이유는 **제네릭 타입이 다른것 만으로는 오버로딩이 성립하지 않아서, 메서드 중복 정의 문제로 컴파일 에러가 발생하기 때문이다.**

이럴때 사용할 수 있는 것이 `?` 를 이용한 와일드 카드이다.

<br>

```java
public class Test {
    public static void main(String[] args) {
        Ex.getName(new Family<Dad>());
        Ex.getName(new Family<Son>());
    }
}

class Ex{
    static void getName(Family<? extends Dad> family) {
    }
}
class Family<T extends Dad>{
    T value;
}

class Son extends Dad{
}
class Dad{
}
```

위와 같이 파라미터에 `<? extends Dad> ` 로 명시해주었고, 이는 Dad를 상속하는 클래스로 타입을 지정한 제네릭 클래스를 받을 수 있음을 의미한다.

`<? super Dad>` 와 같이 `super` 키워드를 사용하면 Dad와 Dad클래스의 조상 클래스로 제한할 수 있다.


<br>

{: .highlight-title }
> 참고한 글
>
> 1. 자바의 정석