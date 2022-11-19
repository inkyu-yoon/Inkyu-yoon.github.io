---
layout: post
title: "· HTTP 상태 코드"
nav_order: 7
parent : HTTP
grand_parent: 📚Learned
permalink: docs/Learned/HTTP/HTTPHeader
---

# HTTP 헤더
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---




## 표현 헤더

<br>

- HTTP 전송에 필요한 모든 부가정보 (메시지 바디의 내용, 크기, 압축, 인증 , 요청 클라이언트, 서버 정보, 등등)

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221108164326483.png" alt="image-20221108164326483" style="zoom:80%;" />
</p>

- 표현 헤더는 표현 데이터를 해석할 수 있는 정보를 제공한다. (데이터 유형, 데이터 길이, 압축 정보..등등)



1. Content-Type

- 표현 데이터의 형식 설명
- 미디어 타입, 문자 인코딩.. 등등

2. Content-Encoding

- 표현 데이터를 압축하기 위해 사용
- 데이터를 **전달하는 곳에서 압축 후** 인코딩 헤더 추가
- 데이터를 **읽는 쪽에서 인코딩 헤더의 정보로 압축 해제**

3. Content-Language

- 표현 데이터의 자연 언어 (예시. ko , en ...)

4. Content-Length

- 표현 데이터의 길이 정보 (분할 전송 `Transfer-Encoding: chunked` 사용 시에는 사용하지 않는다.)



## 협상 헤더

<br>

클라이언트가 선호하는 표현 **요청**, 즉 **요청시에만 사용**한다.

- Accept

- Accept-Charset

- Accept-Encoding

- Accept-Language

- 다중 언어를 지원하는 서버한테 GET 요청을 보낼 때, `Accept-Language:ko`를 함께 보내면, `Content-Language`를 ko로 받을 수 있다.

  > 💡 대신 지원 안되는 언어를 서버에 요청한 경우, 이상한 언어로 응답 받을 수 있기 때문에, q를 이용해서 우선순위를 함께 제공하는 것이 좋다.
  >
  > (예시. `Accept-Language: ko-KR,ko;q=0.9;en-US;q=0.8;en;q=0.7` 과 같이 보낼 수 있다.)
  >
  >
  >
  > 또한, 구체적인 조건이 붙을 수록 우선순위가 높고, 구체적인 조건이 없는 경우 구체적인 조건의 미디어 타입을 맞춘다.
  >
  > `Accept: text/*;q=0.3,text/html;q=0.7` 로 보낼때, text/html은 구체적인 조건이 붙었으므로 q=0.7이 적용되고, text/plain의 경우 구체적인 조건이 없으므로 q=0.3 조건이 적용될 것이다.

<br>

## 일반 정보 헤더



### 1. Referer

- 이전 웹 페이지 주소를 나타낸다.
- Referer를 이용해서 유입 경로 분석이 가능하다.

### 2. User-Agent

- 클라이언트의 애플리케이션 정보
- 어떤 종류의 브라우저에서 장애가 발생하는지 파악이 가능하다.

<br>

## 특별한 정보

<br>

### 1. Host

- 하나의 서버가 여러 도메인을 처리하고 있는 경우, 필요한 호스트 정보를 요청하기 위해 사용한다.



### 2. Location

- 3xx 응답 결과에 Location 헤더가 있으면, Location 위치로 리다이렉트하게 된다.

<br>

## 쿠키

- Cookie : 클라이언트가 서버에서 받은 쿠키를 저장하고, HTTP 요청시 서버로 전달
- Set-Cookie : 서버에서 클라이언트로 쿠키 전달(응답)
- 한번, Set-Cookie 가 되면, 다시 요청할 때, Cookie에서 데이터를 탐색한다.

> - 주민등록번호와 같이 보안에 민감한 데이터는 저장하면 안된다.
> - 사용자 로그인 세션 관리, 광고 정보 트래킹에 사용된다.
>
> -> 쿠키 정보는 항상 서버에 전송되기 때문에, 네트워크 트래픽 추가를 유발하므로 최소한의 정보만 사용한다.



