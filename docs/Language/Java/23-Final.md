---
layout: post
title: "·  JAVA final 키워드의 역할"
nav_order: 23
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/Final
---

#  JAVA final 키워드의 역할
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

{: .important-title}
> final 키워드 핵심!
> 
> final 키워드는 수정될 수 없음을 의미한다.
> 
> 변수에 붙은 경우 다른 값으로 초기화할 수 없다.
> 
> 메서드에 붙은 경우는 상속받은 클래스가 오버라이딩할 수 없다.
> 
> 클래스에 붙은 경우는 클래스를 상속받을 수 없다.


<br>


## JAVA final 키워드 정리



### 변수에 붙이는 경우

변수를 수정할 수 없음을 의미한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230411183125896.png" alt="image-20230411183125896" style="zoom:50%;" />
</p>

따라서 **①번**처럼 final이 붙은 변수는 초기화 값을 꼭 넣어주거나 객체 안에 있는 맴버라면 생성자를 통해 초기화를 시켜주지 않으면 컴파일 에러를 일으킨다.

**②번**처럼 객체에 붙이는 경우는 객체 내의 final 이 붙지 않은 맴버 num은 변경할 수 있지만, 객체 자체를 재 할당하는 것이 제한된다.

**③번**의 경우는 final로 초기화한 a의 값을 변경하려고 해서 컴파일 에러를 일으켰다.

<br>

### 메서드에 붙이는 경우

메서드에 붙이게 되면, 해당 클래스를 상속한 클래스는 final이 붙은 메서드의 Override를 제한한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230411183430276.png" alt="image-20230411183430276" style="zoom:80%;" />
</p>

위와 같이 **final로 선언된 print() 메서드를 오버라이딩 하려고 하니 컴파일 에러가 발생**했다.

<br>

### 클래스에 붙이는 경우

클래스에 붙이게 되면 상속 불가능한 클래스가 된다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230411183531092.png" alt="image-20230411183531092" style="zoom:80%;" />
</p>

위와 같이 **final 키워드가 붙은 클래스를 상속하려고 하면 컴파일 에러가 발생**하는 것을 확인할 수 있다.

final 키워드가 붙은 클래스는 대표적으로 Integer와 같은 Wrapper 타입 클래스, String 클래스가 있다.

<br>



{: .highlight-title }
> 참고한 블로그
>
> 1. [https://sabarada.tistory.com/148](https://sabarada.tistory.com/148)










