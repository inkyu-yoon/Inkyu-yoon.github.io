---
layout: post
title: "· Static import"
nav_order: 9
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/StaticImport
---

# Static import문을 이용한 코드 간결화

<br>

{% capture some_var %}
```java
class Main{
    public static void main(String[] args) {
        System.out.println("Hello World");
        System.out.println("Hello World");
        System.out.println("Hello World");
        System.out.println("Hello World");
        System.out.println("Hello World");
        System.out.println("Hello World");
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


먼저, import 문의 구성은



`import java.패키지명.클래스명.메서드명;` 으로 선언을 한다.



위와 같은 코드가 있다고 가정할 때,



System.out.println 의 사용이 매우 빈번한데, 이를 static import 문을 통해 간결화 시켜줄 수 있다.



`import static java.패키지명.클래스명.*;` 선언을 하면,



특정 클래스의 메서드를 사용할 때, 클래스의 이름을 생략할 수 있다.

{% capture some_var %}
```java
import static java.lang.System.*; //static import 문
class Main{
    public static void main(String[] args) {
        out.println("Hello World");
        out.println("Hello World");
        out.println("Hello World");
        out.println("Hello World");
        out.println("Hello World");
        out.println("Hello World"); // 원래는 System.out이지만, System 생략 가능
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


코드가 너무 복잡해지거나 간결화가 필요할 경우에 static import 문을 사용하면 유용할 것 같다.