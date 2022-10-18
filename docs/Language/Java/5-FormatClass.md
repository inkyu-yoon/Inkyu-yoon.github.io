---
layout: default
title: "형식화 클래스 정리"
nav_order: 5
parent : Java
grand_parent: Language
permalink: docs/Language/Java/FormatClass
---



# 형식화 클래스 DecimalFormat / SimpleDateFormat / ChoiceFormat / MessageFormat 에 대한 이해

<br>

### 형식화 클래스



- 데이터를 패턴에 맞춰 형식화 할 수 있을 뿐만 아니라 역으로 형식화된 데이터에서 원해의 데이터를 얻어낼 수도 있다.

------

### DecimalFormat



- DecimalFormat은 숫자를 형식화 하는데 사용된다


```
DecimalFormat df = new DecimalFormat("형식");
//DeciamlFormat 클래스를 생성하고
//숫자를 어떤 형식의 문자열로 받고싶은지 입력한다.
String result = df.format(숫자);
// format 메서드를 통해 형식에 맞춰 변형된 숫자를 문자열 result로 얻는다.
```



- 숫자를 원하는 형식의 문자열로 출력하기


{% capture some_var %}
```java
import java.text.*;
public class Main {
    public static void main(String[] args) {
        DecimalFormat df1 = new DecimalFormat("0.000");
        //형식을 소숫점 3째자리까지 표현하고 공백이 생기면 0을 채워넣게 설정
        DecimalFormat df2 = new DecimalFormat("0.000E0");
        // 형식을 부동소수점과 4개의 숫자로 지수 형식으로 표현할 수 있도록 설정
        double num = 1234567.89;


        String num1 = df1.format(num);
        //num1이라는 문자열에 df1에서 지정한 형식을 적용
        String num2 = df2.format(num);
        //num2이라는 문자열에 df2에서 지정한 형식을 적용

        System.out.println(num1);
        //결과는 1234567.890이 나옴
        System.out.println(num2);
        //결과는 1.235E6이 나옴

    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


- 문자열 데이터를 숫자로 추출하는 방법, 문자열을 Number 클래스로 parse 하여 거쳐가야 한다. 문자열 추출할때에는 항상 예외처리를 해준다. 만약 문자열로 "1,234,567.89" 가 있다고 가정했을 때.


{% capture some_var %}
```java
import java.text.*;

public class Main{
    public static void main(String[] args) throws Exception{
        //예외처리를 해주어야 한다.

        String strnum = "1,234,567.89";
        DecimalFormat df = new DecimalFormat("#,###.##");
        //바꿀 문자열의 형식을 적용해둔다.
        //형식이 일치하지 않으면 결과를 제대로 출력하지 못한다.

        Number num = df.parse(strnum);
        //parse 메서드를 사용하여 Number 클래스로 변환을 해준다.

        double d = num.doubleValue();
        //Number 클래스의 doubleValue를 사용하여 double형으로 추출한다.

        System.out.println(d);
        //1234567.89 로 출력이 된다.


    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


- 예제 ( 이 예제를 혼자 해보면 추출과 형식 적용 이해가 아주 좋을 것)
  문자열 "1,23,45,67.89" 를 1234567.12 숫자로 추출한 후 "1,234,567.89"문자열로 저장하여라


{% capture some_var %}
```java
import java.text.DecimalFormat;
public class Main {
    public static void main(String[] args) throws Exception {
        String first = "1,23,45,67.89";
        System.out.println("First: " + first);
        //문자열 초기값

        DecimalFormat df = new DecimalFormat("#,##.##");
        Number num = df.parse(first);
        double second = num.doubleValue();
        System.out.println("Second: "+second);
        //문자열의 형식을 이용하여 double로 추출


        DecimalFormat df2 = new DecimalFormat("#,###.##");
        String third = df2.format(second);
        System.out.println("Third: "+third);
        // 다시 형식을 적용시켜 문자열로 변환
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


------

### SimpleDateFormat



- Date와 Calendar클래스를 이용하여 날짜 데이터를 원하는 형태로 쉽게 출력할 수 있다.


{% capture some_var %}
```java
import java.text.SimpleDateFormat;
import java.util.Date;

public class DateFormatEx1 {
    public static void main(String[] args) {
        Date today = new Date();
        // Date 객체를 먼저 생성한다.
        // 만약 Calendar 에서 시작하려면 Calendar를 date로 변환한 후 사용
        //Calendar cal = Calendar.getInstance();
        //Date today = cal.getTime();

        SimpleDateFormat sdf;
        //SimpleDateFormat 선언

        sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss 오늘은 E요일입니다.");
        // 생성하고 형식 지정,
        //y는 년도
        //M은 월
        //d는 일
        //H는 시간
        //m은 분
        //s는 초
        //E는 요일
        System.out.println(sdf.format(today));
        //"20XX-XX-XX XX:XX:XX 오늘은 X요일입니다." 로 출력될 것.
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


- SimpleDateFormat을 이용하여 형식을 갖춘 날짜 문자열을 추출할 수 있다.


{% capture some_var %}
```java
import java.text.SimpleDateFormat;
import java.util.Date;

public class Main {
    public static void main(String[] args) throws Exception {
        String str = "2022년 8월 20일";
        // "2022년 8월 20일 이라는 문자열이 있을때,
        // "yyyy년 MM월 dd일"
        // SimpleDateFormat의 형식을 갖음을 알 수 있따.

        SimpleDateFormat sdf = new SimpleDateFormat("yyyy년 MM월 dd일");
        //SimpleDateFormat 선언


        Date d = sdf.parse(str);
        // parse 메서드를 이용하여 Date 클래스로 변환한다.


        SimpleDateFormat df2 = new SimpleDateFormat("yyyy-MM-dd");
        System.out.println(df2.format(d));
        //다시 변환한 Date 를 SimpleDateFormat을 사용하여 새로운 형식으로 변환한다.


    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

------

### ChoiceFormat



- 특정 범위에 속하는 값을 문자열로 변환해준다.
- 두개의 배열을 생성하는데, 하나는 오름차순의 double형 경계값 배열과 경계값 배열의 크기와 동일한 String 배열이 필요하다. 경계값을 기준으로 해당하는 문자열을 반환하는 것이다.


```
ChoiceFormat 이름 = new ChoiceFormat(경계값배열,반환 문자열 배열);
```


로 생성한다.

{% capture some_var %}
```java
import java.text.*;

public class main{
    public static void main(String[] args){

        double[] limits = {60,70,80,90,};
        //double 형으로 반드시 오름차순
        String[] grade = {"D","C","B","A"};
        //경계값 배열과 수가 일치하는 문자열 배열
        //60~69는 D 70~79는 C 80~89는 B 90~ 은 A를 반환할 것이다.
        int[] score = {100,95,88,70,52,60,70};

        ChoiceFormat cf = new ChoiceFormat(limits,grade);
        //choiceformat 생성
        for(int i=0;i<score.length;i++){
            System.out.println(score[i]+" :" + cf.format(score[i]));
        }


    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

------

### MessageFormat


- 데이터가 들어갈 자리를 {0} {1} {2}.... 과 같이 표시하고 다수의 데이터를 같은 양식으로 출력할 수 있도록 도와준다.

{n} 인덱스가 있는 문자열데이터 str과 채워넣을 문자열이 있는 배열 inputstr 이 있을 때



```
String result = MessageFormat.format(str,inputstr);
```



위와 같이 사용하면 된다.

![image-20221018143510411](https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018143510411.png)


실행 결과는 아래와 같을 것 이다.

![image-20221018142544448](https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018142544448.png)






------

{: .note-title }
> 정리
>
>
> 1. 형식화 클래스에는 DecimalFormat · SimpleDateFormat · ChoiceFormat · MessageFormat 이 있고, [ 형식화클래스 이름 = new 형식화클래스("각 클래스에 맞는형식"); ] 으로 생성을 해준 후 원하는 문자열 데이터 Str 이 있다고 가정하면, [ String result = 클래스이름.format(Str); ] 각 클래스의 format 메서드를 사용하여 추출해낸다.
>
> 
> 2. 특정 문자열에서 형식을 사용하여 숫자를 추출하고자 할때는, 미리 어떤 형식인지 파악해서 형식화클래스를 생성하고 parse를 이용하여 (DecimalFormat의 경우 Number / SimpleDateFormat의 경우 Date / MessageFormat의 경우 Object) 각 형식화 클래스에 적용할 수 있는 클래스로 변환하여 추출한다.