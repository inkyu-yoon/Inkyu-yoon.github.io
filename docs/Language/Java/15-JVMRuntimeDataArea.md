---
layout: post
title: "· JVM 메모리 구조 (RunTime Data Area)"
nav_order: 15
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/JVMRuntimeDataArea
---

# JVM 메모리 구조 (RunTime Data Area)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

{: .important-title}
> 핵심
> JVM 의 메모리구조는 Method Area · Heap · JVM Stack · Native Method Stack · PC Registers 로 구성되어 있습니다.
> 
> Method Area와 Heap 영역은 모든 쓰레드가 공유하는 영역이고 JVM Stack, Native Method Stack, PC Register 는 스레드 별로 각각 갖고 있습니다.
> 

<br>

{: .important-title}
> 각 영역이 하는 역할은 무엇인가요?
> 
> Method Area는 클래스 혹은 인터페이스의 정보와 상수, 메서드 그리고 정적변수를 보관합니다.
> Heap 은 동적으로 생성되는 인스턴스 객체와 배열이 저장되는 공간이며 GC에 의해 정리되는 영역입니다.
> JVM Stack 은 메서드 호출과 관련된 정보를 저장하는 곳으로 지역 변수, 매개 변수, 리턴 값 등이 저장됩니다.
> Native Method Stack 은 자바가 아닌 다른 언어로 작성된 코드를 위한 영역으로 JVM Stack 과 비슷한 역할은 수행합니다.
> PC Register는 JVM의 명령어를 정확하게 제어할 수 있도록 도와주는 역할을 한다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230315131741806.png" alt="image-20230315131741806" style="zoom:67%;" />
</p>

개발자가 `.java` 파일을 만들면 자바 컴파일러는 이 소스 파일을 `.class` 파일로 변환하고, 

클래스 로더는 이 클래스 파일을 바탕으로 JVM 메서드 영역(Method Area)에 동적로딩을 한다.  

그리고 동적 로딩된 데이터를 바탕으로 JIT 컴파일러는 객체를 Heap 영역에 생성하고 Heap 영역에 생성된 객체는 GC를 통해 불필요한 데이터를 정리한다.

이제 JVM이 운영체제로부터 할당받은 메모리 영역인 RunTime Data Area에 대해서 알아보겠다.

위 그림과 같이 **Method Area** · **Heap**· **PC Register** · **JVM Stack** · **Native Method Stack** 로 크게 나눌 수 있다.

RunTime Data Area를 자세히 살펴보면

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230322194414756.png" alt="image-20230322194414756" style="zoom:80%;" />
</p>

위와 같이 볼 수 있고, Heap영역과 Method Area 영역은 스레드간 공유하는 자원이다.

<br>

### Method Area

<br>

클래스 로더는 class 정보를 Method Area에 동적 로딩한다고 했었다.

메모리 공간에 정보가 있어야 실행을 할 수 있으니 필요하다. 프로그램의 흐름을 구성한다고 볼 수 있다.

동적로딩으로 올려지기 때문에, 불필요한 바이트코드를 제외한 프로그램을 실행하는데 필요한 데이터들이 모여있는 영역이라고 할 수 있겠다.

클래스, 인터페이스의 코드와 상수, 정적변수, 메서드 등이 저장된다.

저장되는 데이터는 다음과 같다.

1. **Type Information**
    1. Type 명 : Package name + Class name
    2. Type 종류 : Type이 Class 인지 Interface인지
    3. Type 제어자 : 접근 제어자(public, private, default) 등
    4. 연관된 Interface 정보
2. **Runtime Constant Pool** : Type의 상수 정보를 저장하는 Pool 이다. 인덱스를 통해 접근할 수 있다.
    1. Type, Field, Method로 접근하기 위한 Reference 정보
3. **Field Information** : 인스턴스 변수의 정보
    1. Type명 : 인스턴수 변수의 타입
    2. 제어자
4. **Method Information** : 메서드의 모든 정보
    1. Method 명
    2. Method 반환 타입
    3. Method parameter 정보
5. **Class Variable** : Static 키워드로 선언된 변수 저장



<br>

### Heap

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317133812292.png" alt="image-20230317133812292" style="zoom:80%;" />
</p>

인스턴스가 생성되는 공간으로 위와 같이 GC가 관리하는 공간이다. 참고로 실행엔진 게시글에서 설명했듯이, G1 GC 부터는 Region으로 관리한다.

Heap 영역이 가득 차게 되면 OutOfMemoryError가 발생된다.



<br>

### Java Stack

<br>
스레드의 제어를 위해 사용되는 메모리 영역이다.

각 스레드별로 메모리가 할당되어 있어 동시성 문제에서 자유롭다는 특징이 있다.

스레드가 Method를 호출하면 메서드 호출 주소, 매개 변수, 지역 변수, 연산 스택이 Frame 단위로 stack에 저장된다.

```java
public class Main {
    public static void main(String[] args) {
        int port = 4000;
        String host = "localhost";
    }
}
```

위 코드의 main 메서드가 실행된다고 생각하면 아래 그림과 같다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230322203231163.png" alt="image-20230322203231163" style="zoom:80%;" />
</p>

**객체의 경우 Stack 영역에는 Heap 영역에 생성되어 있는 데이터의 참조값이 할당되고, 원시타입의 데이터는 참조값이 아닌 실제 값을 할당된다.**



메서드가 끝나면 시작할떄 저장했던 지역 변수를 삭제하기 때문에, 메서드 외부에서 사용할 수 없는 것이다.


Java Stack영역이 가득 차게 되면 StackOverflowError가 발생된다.

<br>

### PC Register

<br>

현재 실행 중인 명령어의 주소를 저장하는 역할을 한다. 그리고 다음에 실행할 명령어를 가리키는 역할을 한다.

<br>

### Native Method Stack

<br>

Java 외의 언어로 작성된 네이티브 코드(C 혹은 C++)를 위한 stack이다.

네이티브 메서드 스택은 네이티브 메서드 호출 시 인수, 지역 변수, 리턴 값 등을 저장한다.

자바 코드와 네이티브 코드 간의 상호작용을 지원하기 위해 필요하다.  

이를 통해 자바 언어의 간결성과 이식성을 유지하면서도 네이티브 코드를 활용하여 하드웨어나 운영체제에 특화된 작업을 수행할 수 있다.  

예를 들어, 네이티브 메서드를 사용하여 그래픽 처리, 네트워크 통신, 파일 시스템 접근 등의 작업을 자바 프로그램에 통합할 수 있다.


<br>

{: .highlight-title }
> 참고한 블로그
>
> 1. [https://yaboong.github.io/java/2018/05/26/java-memory-management/](https://yaboong.github.io/java/2018/05/26/java-memory-management/)
> 2. [https://seunghyunson.tistory.com/23](https://seunghyunson.tistory.com/23)



















