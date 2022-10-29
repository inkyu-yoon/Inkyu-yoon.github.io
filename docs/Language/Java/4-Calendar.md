---
layout: post
title: "· Calendar class 정리"
nav_order: 4
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/Calendar
---

# Calendar class 정리
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



## Calendar 클래스

<br>

- Calendar 클래스는 추상 클래스이므로 인스턴스를 생성할 수 없고, `getInstance()`를 사용해서 객체를 생성해야 한다.


```
Calendar cal = Calendar.getInstance();
```




- 원래 날짜와 시간을 다룰 목적으로 제공되었던 Date 클래스는 잘 사용되지 않지만, Calendar로 변환하거나 Calendar를 Date로 변환할 줄 알아야 한다.



### 1. Calendar를 Date로 변환


```
Calendar cal = Calendar.getInstance(); // Calendar 클래스 호출
Date day = cal.getTime(); // Date 클래스로 변환
```


### 2. Date를 Calendar로 변환


```
Date d = new Date(); //Date 클래스 객체 생성
Calendar cal = Calendar.getInstance(); // Calendar 클래스 호출
cal.setTime(d); // Calendar의 시간을 d로 맞춤
```



------

## Calendar 클래스의 사용

<br>

{% capture some_var %}
```java
import java.util.Calendar;

class Main{
    public static void main(String[] args){

    Calendar cal = Calendar.getInstance(); 

    //먼저 Calendar 클래스를 cal 이라는 이름으로 호출하고

    System.out.println(cal.get(Calendar.(static 상수)));
    //위와 같은 방식으로 사용하면 되고, static 상수 자리에 들어가는 종류는 매우 다양하다.

    System.out.println(cal.get(Calendar.YEAR)); 
    //년도 
    System.out.println(cal.get(Calendar.MONTH)); 
    //월 : 0~ 11 (11=12월)
    System.out.println(cal.get(Calendar.WEEK_OF_YEAR)); 
    //이 해의 몇째 주인지
    System.out.println(cal.get(Calendar.WEEK_OF_MONTH)); 
    //이 달의 몇째 주인지
    System.out.println(cal.get(Calendar.DATE)); 
    //몇 일인지
    System.out.println(cal.get(Calendar.DAY_OF_MONTH)); 
    //이 달의 몇일인지
    System.out.println(cal.get(Calendar.DAY_OF_YEAR)); 
    //이 해의 몇일인지 
    System.out.println(cal.get(Calendar.DAY_OF_WEEK)); 
    //요일 1~7 (1:일요일)
    System.out.println(cal.get(Calendar.HOUR)); 
    //시간 (0~11)
    System.out.println(cal.get(Calendar.HOUR_OF_DAY)); 
    //시간(0~23)
    System.out.println(cal.get(Calendar.MINUTE)); 
    //분
    System.out.println(cal.get(Calendar.SECOND)); 
    //초
    System.out.println(cal.getActualMaximunm(Calendar.DATE)); 
    //이 달의 마지막 일
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


Calendar 클래스를 생성하면 기본값으로 오늘 날짜와 시간이 저장된다.



`cal.get()`이 데이터를 읽어오는 것이라면,



`cal.set()`으로 데이터를 저장할 수 있다.



만약 2022-03-01의 날짜를 가진 Calendar 클래스를 생성하고 싶다면 아래와 같이 하면 된다. (Month 필드는 1월이 0)

{% capture some_var %}
```java
import java.util.Calendar;

class Main{
    public static void main(String[] args){

    Calendar cal = Calendar.getInstance();

    cal.set(2022,2,1);

    System.out.println(cal.get(Calendar.YEAR));
    //2022이 출력될 것
    System.out.println(cal.get(Calendar.MONTH));
    //2이 출력될 것
    System.out.println(cal.get(Calendar.DATE));
    //1이 출력될 것
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


`cal.add()` 와 `cal.roll()`을 사용하면 원하는 필드에 데이터를 추가하거나 뺄 수 있다.


{% capture some_var %}
```java
import java.util.Calendar;

class Main{
    public static void main(String[] args){
    Calendar cal = Calendar.getInstance();
    cal.set(2022,0,31);//2022년 1월 31일로 지정했을때 (MONTH 필드는 0부터 시작하므로 0이 1월이다.)

    cal.add(Calendar.YEAR,1);
    //YEAR에 +1 을 해준다.
    cal.add(Calendar.MONTH,-1);
    //MONTH 에 -1 을 해준다.

    cal.roll(Calendar.DATE,1);
    //roll이므로 MONTH에는 영향을 주지 않고 DATE만 1로 변할 것
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

add는 예를 들어 7월31일 인 상황에서 DATE필드에 +1을 해주면 MONTH 필드에도 영향을 주어 8월1일이 되지만,



roll필드는 다른 필드에는 영향을 주지 않아서 7월1일이 된다.