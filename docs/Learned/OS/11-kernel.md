---
layout: post
title: "· 커널이란?"
nav_order: 11
parent : 운영체제
grand_parent: 📚Learned
permalink: docs/Learned/OS/kernel
---

# 커널이란?
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


{: .important-title}
> 핵심
> 
> 커널은 프로세스 관리, 메모리 관리, 저장장치 관리와 같은 운영체제의 핵심적인 기능을 모아놓은 것으로 운영체제의 핵심 부분입니다.

<br>



## 커널

커널은 프로세스 관리, 메모리 관리, 저장장치 관리와 같은 **운영체제의 핵심적인 기능을 모아놓은 것**으로, 자동차에 비유하자면 엔진에 해당한다.

운영체제의 핵심 부분으로 메모리에 상주하는 부분이다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230109101313814.png" alt="image-20230109101313814" style="zoom:80%;" />
</p>

운영체제의 자원은 사용자나 응용 프로그램으로부터 보호하기 위해, 직접 접근할 수 없고 시스템 호출을 통해서만 접근이 가능하다.

두 응용 프로그램이 각자 자신이 원하는 위치에 데이터를 저장하려고 하면, 지워지는 데이터가 생길 것이다.

따라서, 운영체제에게 시스템 호출을 통해 저장을 부탁하면 적절한 위치에 저장해줄 수 있다.

<br>

운영체제는 커널이 제공하는 서비스를 시스템 호출로 제한하고, 다른 방법으로 커널에 들어오지 못하게 막음으로서 컴퓨터 자원을 보호하는 것이다.

<br>


## 커널의 종류

<br>

### 1. 단일형 구조 커널

<br>

- 커널의 핵심 기능을 구현하는 모듈들이 구분없이 하나로 구성되어 있다. 프로그램에 비유하면 함수를 거의 사용하지 않고 main()에 모든 기능을 구현한 형태와 같다.

    - 장점 : 모듈 간의 통신 비용이 줄어 효율적인 커뮤니케이션과 운영이 가능하다.
    - 단점 :
        1. 모든 모듈이 하나로 묶여 있기 때문에, 버그나 오류를 처리하기가 어렵고 작은 결함이 시스템 전체로 확산될 수 있다.
        2. 운영체제가 커지고 복잡해질수록 구현하기가 매우 어렵다.

<p align="center">
  <img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230110132443697.png" alt="image-20230110132443697" style="zoom: 67%;" />
</p>

<br>

### 2. 계층형 구조 커널

<br>

- 비슷한 기능을 가진 모듈을 묶어서 하나의 계층으로 만들어 구현한다.
    - 장점 : 비슷한 기능을 모아 모듈화 했기 때문에 단일형 구조보다 버그나 오류를 쉽게 처리할 수 있다.
    - 단점 :
        1. 하나의 계층에 이상이 생기면, 다른 계층도 동작하지 못할 수 있다.
        2. 계층형 커널 역시, 운영체제가 커지고 복잡해지면 많은 계층들이 발생하게 되어 커널의 크기가 커지고 하드웨어 용량도 늘어난다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230110133010205.png" alt="image-20230110133010205" style="zoom:75%;" />
</p>

<br>

### 3. 마이크로 구조 커널

<br>


- 마이크로 구조 커널의 운영체제는 **프로세스 관리, 메모리 관리, 프로세스 간 통신 관리 등 커널의 가장 기본적인 기능만 제공한다.** 그 외의 기능은 사용자 공간으로 옮겨서 사용자 영역에서 수행하도록 하였다.
- 장점 : 각 모듈은 독립적으로 작동하기 때문에, 하나의 모듈에 문제가 발생해도 전체 **운영체제가 멈추지 않는다.** (커널 내부에서 발생 지연이 적기 때문에 실시간 시스템에 활용하기 좋다.)
- 단점 : 모듈 간 통신이 빈번하게 발생할 시, 성능이 떨어질 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230110133436266.png" alt="image-20230110133436266"  />
</p>

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230110134040493.png" alt="image-20230110134040493" style="zoom:67%;" />
</p>

프로세스 관리, 메모리 관리 등과 같이 반드시 커널에 있어야 하는 기능만 모듈로 남아있는 구조가 마이크로 구조이다.


