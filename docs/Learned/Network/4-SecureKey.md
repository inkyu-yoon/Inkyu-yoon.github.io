---
layout: post
title: "· SSL HandShake와 대칭키 · 비대칭키 방식"
nav_order: 4
parent : Network
grand_parent: 📚Learned
permalink: docs/Learned/Network/SecureKey
---

# SSL HandShake와 대칭키 · 비대칭키 방식



## HTTP 와 HTTPS 의 차이점

<br>

http는 애플리케이션 레벨의 프로토콜이고 기본적으로 80포트를 사용합니다. HTTP는 Method, Path, Version, Headers, Body로 구성됩니다.

암호화 되어있지 않는 평문 데이터를 전송하는 프로토콜이기 때문에, 보안상 취약점이 있었고 이를 해결하기 위해 HTTPS가 나타났습니다.

Https는 기본적으로 443포트를 사용하고 서버와 브라우저 사이에 TLS나 SSL(Secure Socket Layer) 프로토콜로 데이터를 암호화해 통신을 합니다.

<br>


## Https SSL Handshake란

<br>


SSL은 암호화 기법으로 대칭키 방식과 비대칭키 방식을 사용합니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230223222741693.png" alt="image-20230223222741693" style="zoom: 50%;" />
</p>

대칭키 방식은 동일한 키로 암호화와 복호화를 수행하는 방법입니다.

같은 키를 가지고 있어 **대칭키**라 불리는 것입니다.

암호화 복호화 처리에서는 속도가 빠르다는 장점이 있지만, 누군가 키를 탈취해가면 보안상 취약해진다는 단점이 있습니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230223223048710.png" alt="image-20230223223048710" style="zoom:50%;" />
</p>

비대칭키 방식은 서로 다른 키로 복호화를 수행하는 방식입니다.

공개키로 암호화한 데이터는 개인키로만 복호화 할 수 있고, 개인키로 암호화한 데이터는 공개키로만 복호화할 수 있습니다.

따라서 누군가 공개키를 탈취해간다고 해도, 공개키로 암호화한 데이터를 복호화할 수 없습니다.

단, 비대칭키 방식은 대칭키 방식보다 보안상 유리하지만 암호화 연산 시간이 더 소요되어 비용이 크다는 단점이 있습니다.



SSL handshake 는 단점을 보안하기위해, 대칭키 방식과 비대칭키 방식을 혼합해서 사용합니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230223223914314.png" alt="image-20230223223914314" style="zoom: 67%;" />
</p>

간략하게 설명하면, 서버는 자신이 검증된 서버라는 것을 증명하기 위해 CA(인증기관)에 심의를 받고 SSL인증서를 얻을 수 있습니다.

따라서, 사용자는 안전한 통신을 위해서 서버에게 SSL 인증서를 요구하고, 서버는 SSL 인증서를 전달합니다.

받은 SSL 인증서가 가짜일 수 있기 때문에, 사용자는 다시 CA에 검증을 받고 검증이 완료되면 서버의 개인키와 짝을 이루는 공개키를 전달합니다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230223224135146.png" alt="image-20230223224135146" style="zoom:67%;" />
</p>

이제, 사용자는 공개키를, 서버는 개인키를 갖고 있기 때문에 데이터를 주고받으면 될 것 같지만?

비대칭키 방식은 보안은 좋지만 그만큼 발생되는 비용이 있기 때문에 여기서 대칭키 방식을 접목합니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230223224355957.png" alt="image-20230223224355957" style="zoom:67%;" />
</p>


사용자는 그 공개키로 자신의 대칭키를 암호화 한뒤, 서버에 전송합니다.

해커가 대칭키를 가로챈다고해도, 공개키로 암호화된 대칭키를 복호화할 수 없습니다.

서버는 개인키로 공개키에 의해 암호화된 대칭키를 복호화 할 수 있습니다.

그렇게 양측이 대칭키를 갖고 있을 수 있게 되고, **대칭키 방식을 활용할 수 있게 됩니다.**



