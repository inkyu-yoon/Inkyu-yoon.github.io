---
layout: post
title: "· JVM Class Loader(클래스 로더) 동작 과정"
nav_order: 13
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/ClassLoader
---

# JVM Class Loader(클래스 로더) 동작 과정
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


<br>

## Class Loader(클래스 로더)

<br>

클래스 로더는 자바 컴파일러가 변환해둔 `.class` 파일을 동적 로딩한다고 했다.

실행 엔진(Execution Engine)이 사용할 수 있도록 JVM 메모리에 적재하는 역할을 한다.

`로딩` · `링크` · `초기화` 단계를 진행한다.

<br>

### 1. 로딩 (Loading)

<br>

`.class` 파일을 바탕으로 JVM `Method Area`(메서드 영역)에 로드한다.

동적 로딩을 하기 때문에, static 변수나 사용하지 않는 클래스는 로드되지 않는다.

즉, **한번에 메모리에 모두 로드하는 것이 아니라 필요한 경우 동적으로 메모리에 로드**한다는 뜻이다.

메서드와 변수 그리고 Class, Interface , Enum 을 구분해서 저장한다. 

Fully-Quailified Class Name (ex.`java.lang.Character$Subset`) 형식으로  저장한다.

로딩이 끝나면 해당 클래스 타입의 객체를 생성하여 메모리의 `Heap` 영역에 저장한다.

{: .note-title }
> 참고
>
> 예를 들어 `.class`  파일에 `Temp` 라는 클래스가 있다고 해도, 사용하지 않으면 로드하지 않는다. 사용 시점에서 로드한다.
>
>
>
> **단, 클래스를 명시적으로 초기화 하지 않아도 로드하는 경우가 있는데,**
>
> 클래스를 초기화 하지 않고 바로 클래스 내부에 있는 static 변수를 호출할 때이다.
>
> `Temp` 라는 클래스에 `static String value` 라는 static 변수가 있다고 가정할 때, `Temp.value` 라는 명령어를 만나게 되면, `Temp` 클래스도 함께 로드된다. (static 메서드나 static 클래스도 마찬가지다.)
>
> 혹은, `Temp` 라는 클래스가 `Parent` 라는 클래스를 상속하고 있다면 부모 객체 먼저 로드한 뒤, 자식 객체를 로드한다.
>
>
>
> **반대로 클래스 내 맴버를 사용하지만, 클래스를 로드하지 않는 경우도 있다.**
>
>  `static final String VALUE` 라는 static final 변수가 있을 때, `Temp.VALUE` 라는 명령어를 만나면, `Temp` 클래스는 로드되지 않는다.
> 그 이유는 상수는 JVM 의 Constant Pool에 따로 저장되어 관리되기 때문이다.
>
> 그리고 `static class INNER`와 같은 내부 클래스가 있을때, `new Temp.INNER()`의 경우에는 인스턴스를 생성할때, 외부 클래스가 꼭 필요한게 아니기 때문에 외부 클래스인 `Temp`를 로드하지 않는다.

<br>

### 2. 링크

<br>

클래스 파일을 사용하기 위해 검증하는 과정이다.

1. Verify(검증) : 클래스가 자바 언어 명세 및 JVM 명세에 명시된 대로 구성되어 있는지 검사한다. 검증을 실패하면 런타임에러 `java.lang.VerifyError` 를 발생시킨다.

   > **검증된 컴파일러가 만든 class 파일이 아닌**, 제 3자가 수동으로 변경한 경우에도 검증을 실패한다. 따라서 악의적인 변경을 방지한다.

2. Preapre(준비) : 클래스가 필요로 하는 메모리를 할당하고 클래스의 필드, 메서드, 인터페이스를 나타내는 데이터 구조를 준비한다.

   > 메모리를 할당하고 static(정적) 필드가 기본값으로 초기화된다.

3. Resolve(분석) : 클래스의 상수 풀 내 모든 심볼릭 레퍼런스를 실제 메모리 레퍼런스로 교체한다.

   > 심볼릭 레퍼런스 : 메모리 번지가 아닌 이름에 의한 참조
   

<br>

### 3. 초기화

<br> 

링크 단계에서 확보한 메모리 영역에 클래스의 static 변수를 명시된 값으로 할당한다.

<br>

## 클래스 로더의 로딩 과정

<br>

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230315131627240.png" alt="image-20230315131627240" style="zoom:80%;" />
</p>

애플리케이션 클래스로더는 자기가 받은 클래스 로딩 요청을 확장 클래스로더에 위임하고,  확장 클래스 로더는 부트스트랩 클래스로더에 위임한다.

그리고, 부트스트랩 클래스로더부터 요청한 클래스가 있는지 확인하고 없으면 자식 클래스에 위임한다.

애플리케이션 클래스로더에서도 찾지 못하면 `ClassNotFoundException` 예외가 발생한다.


{: .note-title }
> 로딩 과정
>
> 1. JVM의 메서드 영역에 클래스가 로드되어있는지 확인하고, 로드되어 있다면 해당 클래스를 사용한다. 존재하지 않는 경우 클래스 로더에 로드를 요청한다.
> 2. 애플리케이션 클래스로더 -> 확장 클래스로더(플랫폼 클래스로더) -> 부트스트랩 클래스로더 로 요청이 위임된다.
> 3. 부트스트랩 클래스로더는 `jdk/jre/lib`에 해당 클래스가 있는지 확인한다. 없으면 확장 클래스로더(플랫폼 클래스로더)에 위임한다.
> 4. 확장 클래스로더(플랫폼 클래스로더)는 `jdk/jre/lib/ext`에 해당 클래스가 있는지 확인하고 없으면 애플리케이션 클래스로더에 위임한다.
> 5. 애플리케이션 클래스 로더는 환경 변수로 지정된 class path에 해당 클래스가 있는지 확인하고 존재하지 않으면 예외를 발생시킨다.
>
> > Class Path (클래스 패스) : JVM이 시스템 모든 폴더를 검사할 수 없으므로 JVM에 파일 경로를 제공해야한다.


<br>

### 1. 부트스트랩 클래스로더

<br>

자바에서 기본적으로 제공하는 API 등과 같은 표준 JDK 클래스들을 로드한다. 

`jre/lib` 디렉토리에 있는 핵심 라이브러리와  `java.lang.Object`, `java.lang.ClassLoader` 와 같은 최소한의 자바 클래스만을 로드한다.

부트스트랩 클래스 로더는 가장 상위 클래스 로더이므로, 운영체제에 맞게 C/C++와 같은 네이티브 코드로 쓰여있다.

<br>

### 2. 확장 클래스로더

<br>

`jre/lib/ext`나 환경변수 `java.ext.dirs`에 지정된 경로(확장 디렉토리)에서 클래스를 로드한다.

**자바 9 이후부터는 확장 매커니즘이 제거되면서 클래스 이름이 `ExtClassLoader` 에서 `PlatformClassLoader`로 변경되었다.**

<br>

### 3. 애플리케이션 클래스로더

<br>

클래스 패스로 지정한 경로에서 클래스를 로드한다.

쉽게 생각하면, 우리가 만든 `.class` 확장자 파일을 로드한다.

<br>

상위 클래스로더가 찾지 못한 클래스를 하위 클래스로더가 찾는 경우가 존재하게 되므로, 상위 클래스로더는 하위 클래스로드가 로드한 클래스를 볼 수 없다.

<br>

{: .highlight-title }
> 참고한 블로그
> 
> 1. [https://blog.hexabrain.net/397](https://blog.hexabrain.net/397)   
> 2. [https://inpa.tistory.com/entry/JAVA-%E2%98%95-JVM-%EB%82%B4%EB%B6%80-%EA%B5%AC%EC%A1%B0-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%98%81%EC%97%AD-%EC%8B%AC%ED%99%94%ED%8E%B8](https://inpa.tistory.com/entry/JAVA-%E2%98%95-JVM-%EB%82%B4%EB%B6%80-%EA%B5%AC%EC%A1%B0-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%98%81%EC%97%AD-%EC%8B%AC%ED%99%94%ED%8E%B8)

