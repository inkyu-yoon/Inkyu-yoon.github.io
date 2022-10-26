---
layout: default
title: "·  HTTP의 특징과 구조"
nav_order: 3
parent : HTTP
grand_parent: 📚Learned
permalink: docs/Learned/HTTP/AboutHTTP
---

#  HTTP의 특징과 구조
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



## 1. 클라이언트 서버 구조

<br>

- Request Response 구조 이다.
- **클라이언트**는 **서버에 요청을 보내고**, 응답을 대기한다.
- **서버**가 요청에 대한 결과를 만들어서 **응답한다.**

  -> 비즈니스 로직, 데이터는 서버에, UI 및 사용성은 클라이언트에서 다뤄야 각자 역할에 집중하기 좋다.

---

## 2. 무상태 프로토콜 (Stateless)

<br>

- 서버가 클라이언트의 상태를 보존하지 않는다. (이전 요청에 대한 정보가 전혀 없음)

  -> 고객과 점원이 대화하는데, 점원이 계속해서 바뀐다고 생각하면 된다.

  -> 서버가 이전 요청을 기억하지 못하기 때문에, 요청을 누적해서 전달해야한다.

  -> 요청 메세지가 길어질 수 있어 데이터를 많이 차지할 수 있지만, **응답 서버를 계속해서 바꿀 수 있다는 엄청난 이점**이 있다.

- 단, 로그인이 필요없는 단순한 서비스 소개 화면은 무상태 설계가 가능하나, 로그인한 사용자의 경우 브라우저 쿠키, 서버 세션 등을 사용해서 상태 유지를 최소한만 사용하게끔 해놓는다.

---

## 3. 비연결성

<br>

- 서버와 클라이언트가 연결되는 동안, 서버는 자원을 계속해서 소모하고 있다.

- 비연결성 서버는 요청과 응답을 주고받은 뒤에는 서버를 끊는다.

  -> 최소한의 자원을 사용할 수 있다.

- HTTP는 기본적으로 연결을 유지하지 않는 모델이다. (1시간 동안 수천명이 서비스를 사용해도, 실제 서버에서 동시에 처리하는 요청은 수십개 이하로 적다.)

<p align="center">
  <img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221026171125266.png" alt="image-20221026171125266" style="zoom:50%;" />
</p>


-> TCP/IP 연결을 다시 맺어야 해서 시간이 추가되고, HTML 자원,자바스크립트 자원, 이미지 자원 등등을 하나씩 응답하게 된다.


<p align="center">
  <img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221026171142994.png" alt="image-20221026171142994" style="zoom:50%;" />
</p>


-> HTTP는 지속연결(Persistent Connections)로 문제 해결, 자원을 다 넘겨줄 때 까지는 연결을 유지시킨다.

---


## 4. HTTP 메세지

<br>

- HTTP 메세지에 모든 것을 보낼 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221026171437279.png" alt="image-20221026171437279" style="zoom:50%;" />
</p>

-> HTTP 메세지의 구조는 위와 같다.



### 1. 시작 라인

시작 라인은 **request-line / status-line** 으로 이루어져 있다.

<br>

**request-line**은 `HTTP메서드 / 요청 대상 / HTTP version` 으로 이루어져 있다.

- HTTP 메서드

  -> 종류(GET, POST, PUT, DELETE 등등)

  -> GET(리소스 조회) POST(요청 내역 처리)

- 요청 대상

  ->절대경로("/"로 시작하는 경로)와 쿼리로 이루어져 있다.

- HTTP의 버전

예시) `GET/search?q=hello&hl=ko HTTP/1.1` : 메서드 + 요청 대상 + HTTP 버전

<br>

**status-line**은 `HTTP version / HTTP 상태 코드 / 이유 문구` 로 이루어져 있다.

- HTTP 상태 코드

  -> 200: 성공, 400: 클라이언트 요청 오류, 500: 서버 내부 오류

- 이유 문구 : 사람이 이해할 수 있는 짧은 상태 설명 글

예시) `HTTP/1.1 200 OK` : HTTP 버전 + HTTP 상태 코드 + 이유 문구



### 2. HTTP 헤더

헤더는 `field-name: field-value` 로 이루어져 있다. (:는 field-name과 붙어있어야 한다. 띄어쓰기가 허용되지 않는다.)

- HTTP 전송에 필요한 모든 부가정보가 들어있다. (바디의 내용, 크기, 클라이언트 정보, 서버 정보 등등의 메타데이터)



### 3. 메세지 body

HTML 문서, 이미지, 영상, 등등 byte로 표현할 수 있는 모든 데이터가 들어있다.

