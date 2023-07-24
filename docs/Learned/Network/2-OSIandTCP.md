---
layout: post
title: "· OSI · TCP/IP 모델과 캡슐화 · 역캡슐화"
nav_order: 2
parent : Network
grand_parent: 📚Learned
permalink: docs/Learned/Network/OSIandTCP
---

# OSI · TCP/IP 모델과 캡슐화 · 역캡슐화
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

## OSI 모델이란?

- 컴퓨터에서 데이터를 전송하기 위해서 컴퓨터 내부에서 하는 여러 가지 일들을 7 계층으로 나눈 것
- 통신이 일어나는 과정을 단계별로 알 수 있기 때문에 특정한 단계에서 이상이 생기면 그 곳만 수정하면 되기 때문에 편리하다.

- 사용하는 기계 , 케이블 등과 관계 없이 통신을 하기 위해 공통으로 사용하기로 약속한 통신 표준 규격이다.

  > **응용 · 표현 · 세션 · 전송 · 네트워크 · 데이터 링크 · 물리** 총 7개의 계층으로 이루어져 있다. 
  > 
  > 현재는, **응용 · 전송 · 인터넷 · 네트워크 접속** 총 4개의 계층으로 이루어진 **TCP/IP** 모델을 사용한다. 
  > 
  > TCP/IP 모델은 OSI 모델을 바탕으로 실생활에 사용하기 위해 만들어졌다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221122154504642.png" alt="image-20221122154504642" style="zoom: 67%;" />
</p>

OSI와 TCP/IP 모델을 비교하면, 위와 같다고 볼 수 있다.



데이터를 송신할 때는, 상위 계층에서 하위 계층으로 데이터를 전달하고, 수신할 때는 하위 계층에서 상위 계층으로 전달한다.

<br>

### 물리 계층

물리 계층은 전기 신호를 전달하는 계층이다.

컴퓨터는 0과 1로 이루어진 비트열을 사용하기 때문에, 이를 전기 신호로 바꾸어주기 위한 기술이다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230724145619241.png" alt="image-20230724145619241" style="zoom:80%;" />
</p>

랜카드는 컴퓨터와 네트워크 간 데이터를 주고받을 수 있도록 해주는 하드웨어이다.

컴퓨터에서 사용하는 디지털 신호를 전기 신호로 변환하여 전송하고 전기 신호를 받아 디지털 신호로 변환하여 전달 받을 수 있도록 한다.

이를 통해, 네트워크를 통해 컴퓨터 간에 파일 전송이나 인터넷 접속 등의 기능을 수행할 수 있게된다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230724150310887.png" alt="image-20230724150310887" style="zoom:80%;" />
</p>

컴퓨터 간 멀리 있을 때는, 신호가 갈수록 약해지기 때문에 **리피터** 라는 장비를 사용해서 전기 신호를 증폭시킨다.

단, 리피터는 1대1 통신만 지원한다. 따라서, 리피터 보다는 **허브** 를 사용한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230724150351136.png" alt="image-20230724150351136" style="zoom:80%;" />
</p>

허브는 감쇠된 신호를 증폭하는 리피터의 역할도 하면서, 1대1 연결이 아닌 기기를 여러개 연결해준다.

한 기기에서 허브로 전달한 데이터는, 연결되어 있는 모든 기기에 전송한다는 특징이 있다.

<br>

### 데이터 링크 계층

데이터 링크 계층은 네트워크 장비 간에 신호를 주고받는 규칭을 정하는 계층이다.

랜에서 데이터를 정상적으로 주고받기 위해 필요한 계층이고, 가장 많이 사용되는 프로토콜로는 **이더넷**이 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230724151124593.png" alt="image-20230724151124593" style="zoom:80%;" />
</p>

여러 기기가 동시에 허브를 통해 데이터를 송신하게 되면 충돌이 발생할 수 있다.

따라서, 데이터를 전송하기 전에 이미 신호가 흐르고 있는지 확인하고 없을 때 전송하는 방식으로 충돌을 피한다.

이러한 방법을 CSMA/CD 라고 하는데 효율상 좋지 않아 현재는 잘 사용되지 않는 방식이고

**스위치** 라는 네트워크 장비를 사용하여 충돌을 방지한다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230724151440022.png" alt="image-20230724151440022" style="zoom:80%;" />
</p>

데이터 링크 계층은 **이더넷** 프로토콜을 사용하는데, 이더넷 헤더에는 목적지 MAC 주소와 출발지 MAC 주소가 있다.

**MAC 주소**는 랜카드에 부여된 전 세계에서 유일한 번호이고, 이를 통해 어떤 기기에 전송할지 식별할 수 있다.

<br>

**스위치** 에 대해 살펴보면, 허브와는 다르게 스위치 내부에는 MAC 주소 테이블이 존재한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230724151947338.png" alt="image-20230724151947338" style="zoom:80%;" />
</p>

MAC 주소 테이블은 스위치 기기의 포트와 그 포트에 연결된 기기의 MAC 주소를 묶어 관리한다.

처음에는 당연히, 각 포트에 연결된 MAC 주소를 알 수 없고 위와 같이 데이터 송신이 발생하면 해당 포트에 MAC 주소가 기록된다.

그리고 도착할 MAC 주소를 모르는 경우에는 일단 연결된 모든 기기에 전송한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230724152215043.png" alt="image-20230724152215043" style="zoom:80%;" />
</p>

만약 도착지 MAC 주소를 아는 경우에는, 위와 같이 해당 MAC 주소를 가진 기기에만 전송하게 된다.

---

## 데이터의 캡슐화와 역캡슐화

<br>

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230724144531466.png" alt="image-20230724144531466" style="zoom: 33%;" />
</p>


- 응용 계층에서 요청 데이터를 만들고, 전송 계층, 네트워크 계층, 데이터 링크 계층 에서 각 계층에서 원활한 데이터 통신을 위해 헤더 및 트레일러 를 붙여나가는 것을 **캡슐화** 라고 한다.

- 수신 하는 과정에서는 역순으로 헤더를 제거하고, 헤더가 모두 제거된 데이터가 수신된다. 이를 **역캡슐화** 라고 한다.

