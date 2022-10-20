---
layout: default
title: "· URI (Uniform Resource Identifier)"
nav_order: 2
parent : HTTP
grand_parent: 📚Learned
permalink: docs/Learned/HTTP/URLFlow
---

#  URI (Uniform Resource Identifier)란?
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## URI 그리고 URL, URN

<br>

- **URI**는 특정 리소스를 식별하는 식별자이다.

- URI에는 **URL(Resource Locator)**와 **URN(Resource Name)** 가 있다.

  **URL**: 리소스의 위치를 나타낸다.
  **URN**: 리소스의 이름을 나타낸다.

  이름만으로는 리소스를 찾을 수 있는 방법이 보편화되어 있지 않기 때문에 URL을 사용한다.
  따라서 URI와 URL은 거의 같은 의미로 볼 수 있다.

- `https://www.inkyu-yoon.com/index.html` 은 URI이자 URL이라고 볼 수 있지만
  `https://www.inkyu-yoon.com/index` 는 URI이지만, 파일 위치의 주소를 가리키지 않기 때문에 URL은 아니라고 할 수 있겠다.

---

## URL 문법

<br>

- `scheme://[userinfo@]host[:prot][/path][?query][#fragment]`

1. scheme(프로토콜) : 어떤 자원방식으로 자원에 접근할 것인가 (http, https등등)

   -> http는 80포트 htps는 443포트를 주로 사용하며 포트는 생략할 수 있다.

2. userinfo: 는 사용자 정보인데, 잘 사용하지 않는다.

3. host : 호스트명, 도메인명 혹은 IP주소 직접 입력

4. port : 접속 포트, 일반적으로 생략 (생략시 http 80 https 443)
5. 리소스 경로 : 일반적으로 계층적 구조로 설계한다. (ex./home/file1.jpg 등등)

6. query : key=value 형태, **?**로 시작, **&**로 추가 가능 웹서버에서 제공하는 파라미터 (쿼리 스트링, 쿼리 파라미터라고도 부름)

7. fragment : html 내부 북마크 등에 사용, 서버에 전송하는 정보는 아님

---

## 웹 요청 흐름

<br>

제일 먼저, host 명으로 DNS를 조회해서 도착지 IP를 확인한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020223205769.png" alt="image-20221020223205769" style="zoom: 33%;" />
</p>

위와 같은 형태의 HTTP 요청 메시지를 생성한다.

그리고 Socket 라이브러리를 통해 TCP/IP 연결 + 데이터 전달한다.

TCP/IP 패킷 정보 생성한 뒤, 인터넷으로 보낸다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020223441679.png" alt="image-20221020223441679" style="zoom:33%;" />
</p>


간단하게 보면, 위와 같은 형태의 정보가 생성된다.

패킷된 HTTP 메세지는 수많은 인터넷 노드를 지나 도착지 IP로 보낸다.

메세지가 도착지 IP에 도착되면, 서버가 HTTP 메세지를 해석한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020223725390.png" alt="image-20221020223725390" style="zoom: 50%;" />
</p>

해석한 뒤 응답 메세지를 만든다.

그리고 패킷을 만들어 다시 응답 메세지를 보낸다.

위의 예시의 경우, 도착한 응답 메세지의 html 파일이 실행되며, 원하는 화면이 나온다.