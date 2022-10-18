---
layout: default
title: "기본형·참조형 매개변수와 Static Method"
nav_order: 3
parent : Java
grand_parent: Language
permalink: docs/Language/Java/VariablesAndStatic
---

# 기본형·참조형 매개변수와 Static Method
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## 매개변수



1. 기본형 매개변수 : 변수의 값을 읽기만 할 수 있다.
2. 참조형 매개변수 : 변수의 값을 읽고 변경할 수 있다.

- 매개변수란 함수에서 대입하는 값이라고 생각하면 쉽다.

---



## class 메서드



- 메서드란 함수의 역할을 한다고 생각하면 된다. 코드의 중복을 없에고 간소화 하는데 도움을 준다.어떤 기능을 하는 메서드를 만들어 놓으면 그 메서드만 호출해서 사용하면 코드의 작성이 간편해지기 때문이다.



- 다른 클래스의 메서드를 사용하기 위해서는, 메서드 앞에 static 유무와 상관없이 그 클래스를 먼저 생성을 하고 그 클래스를 거쳐서 메서드를 사용해야 한다.

---



## 예문



예를 들어 Main과 다른, Data 클래스의 change 메서드를 사용하고 싶을 때,

{% capture some_var %}
```java
class Data {

    int x;  // x라는 인스턴스 변수를 갖는 Data class

    static void change(int x){            // int x 라는 기본형 데이터를 매개변수로 받는 change 메서드
        x = 1000;
    }    
 }


public class Main {
    public static void main(String[] args){

        Data d = new Data(); // Data 객체 생성
        d.x = 100; // d라는 이름의 Data 객체가 갖고 있는 x 변수에 100이라는 값을 저장

        System.out.println(d.x);
        // 100 값을 저장했기 때문에 100이 나올 것

        d.change(d.x); // change 메서드는 Main 클래스 내에 있는 것이 아니고 Data클래스의 메서드이다.
                    //  change 메서드 앞에 static 있든 없든 d라는 객체 생성 이후, 사용 가능.

    }

}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


Data 객체인 d를 거쳐서 d.change() 로 메서드를 사용했다.



같은 클래스의 메서드를 사용하기 위해서는, 메서드 앞에 static 의 유무에 따라 다르다.



먼저 static이 붙은 경우

{% capture some_var %}
```java
class Data {
    int x;
    }
    // x라는 인스턴스 변수를 갖는 Data class

public class Main {

    static void change(int x){            // int x 라는 기본형 데이터를 매개변수로 받는 change 메서드
        x = 1000;
    }    

    public static void main(String[] args){

        Data d = new Data(); // Data 객체 생성
        d.x = 100; // d라는 이름의 Data 객체가 갖고 있는 x 변수에 100이라는 값을 저장

        System.out.println(d.x);
        // 100 값을 저장했기 때문에 100이 나올 것

        change(d.x); // change 메서드는 같은 Main class 내의 메서드이고, static 메서드이므로 별도의 선언 없이 사용가능하다.
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


같은 클래스 내의 메서드 앞에 static이 붙은 경우 별도의 호출 없이 바로 change() 사용할 수 있다.



메서드 앞에 static이 안붙은 경우

{% capture some_var %}
```java
class Data {
    int x;
    }
    // x라는 인스턴스 변수를 갖는 Data class

public class Main {

    change(int x){            // int x 라는 기본형 데이터를 매개변수로 받는 change 메서드
        x = 1000;
    }    

    public static void main(String[] args){

        Data d = new Data; // Data 객체 생성
        d.x = 100; // d라는 이름의 Data 객체가 갖고 있는 x 변수에 100이라는 값을 저장

        Main m = new Main(); // Main 클래스 객체 생성

        System.out.println(d.x);
        // 100 값을 저장했기 때문에 100이 나올 것

        m.change(d.x); // Main 클래스의 change 메서드를 사용하겠다. 의 의미와 같다. 
                       // static이 붙어있지 않기 때문에 호출없이 사용할 수 없다.
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


static이 안붙었기 때문에, Main m 이라는 객체를 생성한 후 m을 거쳐서 m.change() 로 사용하였다.



------



그렇다면 기본형 매개변수와 참조형 매개변수의 차이를 알아보자.



먼저 기본형 데이터는 말 그대로 수치만 갖고 있는 데이터이고 참조형 데이터는 그 수치의 위치를 알고있는 데이터라고 생각하면 쉽다.


{% capture some_var %}
```java
class Data {
    int x;
    }
    // x라는 인스턴스 변수를 갖는 Data class

public class test {

    static void change(int x){            // int x 라는 기본형 데이터를 매개변수로 받는 change 메서드
        x = 1000;
           System.out.println(x); // ②
    }    

    public static void main(String[] args){

        Data d = new Data(); // Data 객체 생성
        d.x = 100; // d라는 이름의 Data 객체가 갖고 있는 x 변수에 100이라는 값을 저장

        System.out.println(d.x); // ①
        // 100 값을 저장했기 때문에 100이 나올 것
        change(d.x); // change 메서드는 같은 Main class 내의 메서드이고, static 메서드이므로 별도의 선언 없이 사용가능하다.

        System.out.println(d.x); // ③
        // change 메서드를 거쳐갔지만 출력은 변경되지 않은 값인 100이 나올 것이다.
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


실행 결과는 아래와 같이 나올 것이다.



```
100  // ① 에 의해 출력
1000 // ② 에 의해 출력
100 // ③ 에 의해 출력
```



기본형 데이터를 매개변수로 갖는 경우 그 데이터를 메서드에서 활용은 할 수 있을 뿐 근본적인 데이터 수정은 할 수 없다.



참조형 데이터를 매개변수로 갖는 메서드의 경우


{% capture some_var %}
```java
class Data {
    int x;
    }
    // x라는 인스턴스 변수를 갖는 Data class

public class test {

    static void change(Data d){            // Data d 라는 참조형 데이터(객체)를 매개변수로 받는 change 메서드
        d.x = 1000;                        // Data d를 변수로 받아 그 d의 인스턴스 변수 x를 1000으로 바꾼다는 의미

    }    

    public static void main(String[] args){

        Data d = new Data(); // Data 객체 생성
        d.x = 100; // d라는 이름의 Data 객체가 갖고 있는 x 변수에 100이라는 값을 저장

        System.out.println(d.x);
        // 100 값을 저장했기 때문에 100이 나올 것
        change(d); // change 메서드는 같은 Main class 내의 메서드이고, static 메서드이므로 별도의 선언 없이 사용가능하다.

        System.out.println(d.x);
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


생성한 객체의 주소를 직접 받아 그 주소로 가서 데이터를 1000으로 바꾸었으니 출력 결과는 1000으로 바뀌어 있을 것이다.



------

{: .note-title }
> 정리
>
> 1. 기본형 데이터를 변수로 사용하는 메서드는 메서드 내에서 활용만 할 수 있고 참조형 데이터를 변수로 사용하는 메서드는 그 객체의 데이터에 직접적인 영향을 줄 수 있다.
>
>
> 2. 다른 class에 존재하는 메서드는 객체를 생성한 후 거쳐서 사용할 수 있고, 같은 class의 메서드의 경우는 메서드 앞에 static이 붙어있으면 그냥 사용할 수 있지만, static이 붙어있지 않은 경우에는 다른 클래스에 존재하는 메서드를 사용하는 방식과 같이, main 클래스를 생성한 후 사용이 가능하다.