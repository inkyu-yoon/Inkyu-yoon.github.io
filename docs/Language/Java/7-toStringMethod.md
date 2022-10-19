---
layout: default
title: "· 10진수를 2진수, 8진수, 16진수로 변환하기"
nav_order: 7
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/toStringMethod
---

# 10진수를 2진수, 8진수, 16진수로 변환하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## 10진수를 8진수, 16진수로 변환하여 출력

<br>

10진수의 수를 8진수, 16진수로 출력하는 방법은 매우 간단하다.



8진수와 16진수로 출력할 수 있는 `printf` 지시자가 있어서 이를 이용하면 된다.



8진수의 경우 `%o` , 16진수의 경우 `%x`를 사용하면 출력할 수 있다.



하지만, 2진수의 경우 위와같은 지시자가 없다.



물론, 조건문, 반복문을 사용하여 10진수를 2진수로 변환하는 메서드를 작성할 수 있지만, 편한 방법이 있다.

------

## toBinaryString

<br>

10진수의 수가 `int`형이라고 가정할때,



`Integer.toBinaryString(Input);` 을 사용하면 된다.



만약, 변환하고 싶은 수의 타입이 `double` 이라면



`Double.toBinaryString(Input);` 을 사용하면 된다.



2진수의 문자열로 변환해주는 toBinaryString 메서드와 같이, 8진수, 16진수의 문자열로 변환해주는 메서드도 존재한다.



`Integer.toHexString(Input);` 과 `Integer.toOctalString(Input);` 을 사용하면 된다.

------

## 예제

<br>

31이라는 10진수의 수가 있을 때, 2진수, 8진수, 16진수로 변환을 해보겠다.


{% capture some_var %}
```java
public class Main {
    public static void main(String[] args) {
        int Num = 31;
        String changeBinary = Integer.toBinaryString(Num);
        String changeOctal = Integer.toOctalString(Num);
        String changeHex = Integer.toHexString(Num);

        System.out.printf("int (10진수) : %d\n",Num);
        System.out.println("=======================");

        System.out.printf("int (8진수) : %o\n",Num);
        System.out.printf("int (16진수) : %x\n",Num);

        System.out.println();

        System.out.println("String (8진수) : " + changeOctal);
        System.out.println("String (16진수) : " + changeHex);
        System.out.println("String (2진수) : " + changeBinary);
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


실행하면, 아래와 같은 결과가 나온다.

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018171543163.png" alt="image-20221018171543163" style="zoom: 80%;" />
</p>

8진수, 16진수의 경우 똑같은 결과값이 나왔다.



하지만, `printf`의 지시자를 활용한 경우 정수의 형식으로 출력되지만, 메서드를 사용한 경우는 문자열이라는 차이가 있다.

------

{: .note-title }
> 정리
>
> 1. 10진수를 8진수, 16진수로 변환하는 방법은, printf의 %o, %x 지시자를 사용하거나 toOctalString(), toHexString() 을 사용한다.
>
> 
> 2. 10진수를 2진수로 변환하는 방법은 printf 지시자가 존재하지 않기 때문에, toBinaryString() 메서드를 사용한다.