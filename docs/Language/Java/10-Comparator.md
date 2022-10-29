---
layout: post
title: "· Comparator 와 Comparable"
nav_order: 10
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/Comparator
---

# Comparator 와 Comparable에 대해 이해하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Collections.sort()

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018173441982.png" alt="image-20221018173441982" style="zoom:67%;" />
</p>


배열을 오름차순하는 가장 쉬운 방법은 Arrays 를 import 해준 뒤,



Arrays의 sort 메서드를 사용하면 알아서 오름차순 정렬을 해준다!



sort 메서드는 `Comparable` 인터페이스를 `Arrays` 클래스에서 구현하여 동작시키도록 한 것이다.


{% capture some_var %}
```java
public interface Comparator{
	int compare(Object o1, Object o2);
    boolean equals(Object obj);
}

public interface Comparable{
	public int compareTo(Object o);
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


`Comparator` 인터페이스와 `Comparable` 인터페이스의 실제 소스코드는 위와 같다.



`Comparator` 인터페이스 안에는 `compare` 메서드와 `equals` 메서드가 있고



`Comparable` 인터페이스 안에는 `compareTo` 메서드가 존재한다.



`compare` 메서드와 `compareTo` 메서드는 기본적으로 `int`값을 반환하도록 되어있고, 

<br>

**음수, 0, 양수** 중의 하나를 반환하도록 구현해야한다. 음수가 꼭 -1이고 양수가 꼭 +1 일 필요는 없고 부호만 잘 맞춰주면 된다.



일반적으로, Compare 메서드는 오름차순으로 정렬하게끔 구현되어 있다.



두 객체를 비교했을 때, 음수값이 리턴되면, 이미 오름차순 정렬이 되어있다는 뜻으로 받아들이고 순서를 바꾸지 않고, 양수값이 리턴되면 두 객체의 위치를 바꿔 오름차순 정렬을 만들어주게끔 구현되어있다.



즉, `Collections` 는 기본적으로 `sort` 메서드를 사용했을 때 오름차순 정렬을 해주기 때문에, 정렬의 기준을 바꾸고 싶다면 Comparator을 implements 해주고 compare 메서드를 오버라이딩 해주거나, Comparable을 implements 해주고 compareTo 메서드를 오버라이딩 해주어 기준을 바꿔주어야 한다.

------

{: .note-title }
> 정리
> 
> **`Comparable`을 오버라이딩 하는 경우는 기준 정렬을 제공하고 싶을때(직접 새로 구현한 객체의 경우 변수 갯수가 다양하기 때문에)!**
>
> **`Comparator`는 기존에 구현되어있는 오름차순 정렬이 아닌 새로운 정렬을 하고 싶을때 구현한다고 생각하면 된다!**
>
> **정말 쉽게 생각하면, if 문을 이용해서 기준을 정하고 양수를 return하면 두 위치를 바꾸고, 음수를 return하면 두 위치를 그대로 냅둔다 라고 생각하면 된다.**

------

## Comparable 구현하기

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018173511692.png" alt="image-20221018173511692" style="zoom: 80%;" />
</p>

위 같은 경우는 `Student` 객체안에 `name` 과 `no` 두개의 변수가 존재하고, 어떤 변수를 기준으로 정렬을 할지 제공해야하기 때문에 Comparable 을 implements 하였다.



그리고 `compareTo()` 메서드를 오버라이딩 하였는데, 매개변수로 들어오는 값은 기본적으로 뒤에 배치되어 있는 데이터를 의미한다.



위에서 설명했듯이, 뒤에있는 데이터와 위치를 바꾸고 싶다면 기준을 세우고 그 기준을 만족했을 때 1을 반환하게끔 구현하면 된다.



위 예시에서는 `.no`를 활용하여 번호를 통해 매개변수로 들어오는 객체(뒤에있는 객체) 의 번호가 더 작다면 양수를 반환하여 두 객체의 위치를 바꾸도록 구현한 것이다.



출력화면을 보면 오름차순 정렬이 된 것을 확인할 수 있다.





------

## Comparator 구현하기

<br>


`Comparator`는 새로운 정렬 방법을 제공하는 것이다.



대표적으로는 내림차순이 있겠다.



`Collections.sort()` 는 매개변수 1개만 넣으면 기본적으로 오름차순을 해주지만,



`Collections.sort(Object[] a, Comparator c)` 와 같이 두번째 매개변수로 `Comparator`를 구현한 클래스를 넣어주면 구현한 기준에 따라 정렬을 하게 된다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018173529715.png" alt="image-20221018173529715" style="zoom:80%;" />
</p>

위와 같은 경우는 기본적으로 구현된 오름차순 정렬의 `Comparator`이다.



따라서 `compare` 메서드의 return 값에 -1을 곱해준다면, 원래 `compare` 메서드의 반대로 동작할 것이다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018173543307.png" alt="image-20221018173543307" style="zoom:80%;" />
</p>


`compare `반환값에 -1을 곱했더니 내림차순으로 정렬이 되었다.




<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018173606297.png" alt="image-20221018173606297"  />
</p>

결론은 양수값이 리턴되면 두 객체의 위치를 바꾸도록 되어있으니, 

<br>

우리가 `Comparable`이나 `Comparator` 를 구현하여 오버라이딩한 다음 적절한 조건식을 넣어서,

<br>

조건문을 통해 해당 조건을 만족하면 양수값을 리턴하도록 하면, 어떤 조건식이 써있든 위치를 바꾸게 동작하기 때문에 원하는 기준으로 정렬을 할 수 있는 것이다!