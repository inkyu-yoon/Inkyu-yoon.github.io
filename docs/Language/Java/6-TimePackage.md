---
layout: default
title: "· Time 패키지 정리"
nav_order: 6
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/TimePackage
---


# Time 패키지정리
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



## Time 패키지 데이터 출력

<br>

- Time 패키지는 Date와 Calendar의 단점들을 해소하기위해 새로 추가되었다. 하지만, 기존에 작성된 프로그램과의 호환성 때문에 Date와 Calendar는 여전히 사용될 것이다.



- time 패키지는 날짜와 시간을 별도의 클래스로 분류해놓았다.


시간을 표현할 때는 `LocalTime time = LocalTime.of(H,m,s);`


날짜를 표현할 때는 `LocalDate date = LocalDate.of(y,M,d);`



- 현재 시간이나 날짜를 사용하고 싶다면 of 대신 now() 를 사용하면 된다.

{% capture some_var %}
```java
import java.time.Duration;
import java.time.LocalDate;
import java.time.LocalTime;
import java.time.Period;

public class NewTimeEx1 {
    public static void main(String[] args) {
        LocalDate today = LocalDate.now();
        LocalTime now = LocalTime.now();
        //현재로 날짜와 시간을 지정

        LocalDate myday = LocalDate.of(1995,12,6);
        LocalTime mytime = LocalTime.of(12,6,30);

        System.out.println(today);
        System.out.println(now);

        System.out.println(myday);
        System.out.println(mytime);

        System.out.println(myday.withYear(2022));
        //수정하고 싶으면 withYear, withMonth, withDayOfMonth 등 메서드 사용
        System.out.println(mytime.withHour(10));
        //수정하고 싶으면 withHour, withMinute, withSecond 등 메서드 사용


        System.out.println(myday.plusYears(-3));
        //수정하고 싶으면 plusYears, plusMonths, plusDays 등 메서드 사용
        System.out.println(mytime.plusHours(-5));
        //수정하고 싶으면 plusHours, plusMinutes, plusSeconds 등 메서드 사용

        Period pe = Period.between(today, myday);
        System.out.println(pe);
        //Period 클래스를 사용하여 LocalDate와 LocalDate간의 차이를 년/월/일 형태로 알려준다.
        System.out.print(pe.getYears()+"년 ");
        System.out.print(pe.getMonths()+"월 ");
        System.out.print(pe.getDays()+"일 ");
        //데이터를 추출할땐 getYears, getMonths, getDays를 사용한다.
        System.out.println();

        LocalTime time1 = LocalTime.of(0,0,0);
        LocalTime time2 = LocalTime.of(12,34,56);

        Duration da = Duration.between(time1, time2);
        System.out.println(da);
        // Duration을 통해 얻은 데이터는 시간/분 단위로 데이터를 추출할 수 있는 메서드가 없으므로 아래와 같이
        // 0,0으로 지정한 LocalTime을 하나 생성하고 더해준 후 추출한다.

        LocalTime timeduration = LocalTime.of(0, 0).plusSeconds(da.getSeconds());
        System.out.print(timeduration.getHour()+"시간 ");
        System.out.print(timeduration.getMinute()+"분 ");
        System.out.print(timeduration.getSecond()+"초 ");

    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


------

## 타임클래스는 DateTimeFormatter 클래스를 활용하여 형식화 한다.

<br>

```
DateTimeFormatter formatter = DateTimeFormatter.형식.format(date)
```


정해진 형식으로 출력하는 방법과


```
LocalDateTime dateTime = LocalDateTime.now();
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("원하는형식");

dateTime.format(formatter);
```


원하는 형식으로 출력하는 방법을 활용한다.

{% capture some_var %}
```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class DateFormatterEx1 {
    public static void main(String[] args) throws Exception{
        LocalDateTime datetime = LocalDateTime.now();

        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy년 MM월 dd일 E요일 그리고 HH시 mm분 ss초 입니다.");
        System.out.println(datetime.format(dtf));

    }

}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

------


{: .note-title }
> 정리
> 1. time 패키지는 LocalTime 과 LocalDate 그리고 LocalDateTime 클래스가 있다. of() 메서드를 활용하여 값을 설정할 수 있고 withYear, withMonth, withDayOfMonth 등의 메서드를 활용하여 데이터를 수정할 수 있다.
>
> 
> 2. Period 와 Duration 클래스로 두 날짜간의 차이나 시간차이를 구할 수 있고 getYears 등등의 메서드를 활용하여 원하는 필드의 데이터를 추출할 수 있지만 Duration의 경우 getSecond만 지원되기 때문에 새로운 0:0:0으로 설정된 LocalTime 객체를 생성하고 더해준 후 추출한다.
>
> 
> 3. DateTimeFormatter 클래스를 활용하면 원하는 문자열로 LocalDateTime 데이터를 출력할 수 있다.