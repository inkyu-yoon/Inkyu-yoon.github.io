---
layout: post
title: "· JVM의 간략한 동작 과정"
nav_order: 12
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/AboutJVM
---

# JVM의 간략한 동작 과정
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
<br>

## JVM의 구성요소

<br>

Java의 가장 큰 특징 중에 하나는 **특정 OS(운영체제)에 종속되지 않는다는 것이다.**

간단하게 말하면, **JVM(Java Virtual Machine)** 이라는 것이 존재하고

자바 응용 프로그램은 운영체제나 하드웨어가 아닌 JVM하고만 통신을 하고 JVM이 운영체제에 맞게 변환하여 전달해주기 때문이다.

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230315131741806.png" alt="image-20230315131741806" style="zoom:67%;" />

</p>



<br>

## java 소스파일의 간략한 실행 과정

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230315131722024.png" alt="image-20230315131722024" style="zoom:80%;" />
</p>

`.java` 파일을 우리가 작성하면 **자바 컴파일러**는 JVM이 이해할 수 있는 **바이트 파일**인 `.class`로 번역한다. (소스파일을 목적파일로 변환)

{: .note-title }
> 참고
> 
> 자바 컴파일러 : javac 라고도 한다. 일반적으로 jdk(자바 개발 키트)안에 포함되어있고, 소스파일이 JVM으로 가기 전 바이트 파일로 변환한다.
>
> 바이너리 코드 : **CPU**가 이해할 수 있는 언어, 0과 1로 구성된 코드  
> 바이트 코드 : **가상 머신**이 이해할 수 있는 바이너리 코드

<br>

<p align = "center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230315131702881.png" alt="image-20230315131702881" style="zoom:80%;" />

</p>

그러면 JVM의 `Class Loader`는 변환된 `.class` 파일을 **운영체제로부터 일정 부분 할당 받은 JVM용 메모리(RunTime Data Area)에 동적로딩**한다.

{: .note-title }
> 참고
>
> 동적 로딩 : 관련되어 있는 클래스를 모두 로딩한 다음 실행하는 것이 아닌, **코드를 실행하는 순간 · 필요한 시점에 클래스를 로딩**한다.

<br>

**실행 엔진(Execution Engine)에** 있는 JIT(Just-in-time) 컴파일러는 **RunTime Data Area**에 로딩된 `.class` 파일을 기계어로 번역하고 실행한다.

{: .note-title }
> 참고
>
> JIT 컴파일러 : 인터프리터와 같이, 코드 실행 시점에서 기계어로 번역하고 변환된 코드를 캐싱한다.  
> 이미 캐싱되어 있는 바이트 코드가 들어올 시, **이전에 번역되어 있는 코드를 사용하여 실행 속도가 느리다는 인터프리터의 단점을 개선**하였다.


