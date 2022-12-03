---
layout: post
title: "· 캐시와 검증헤더"
nav_order: 8
parent : HTTP
grand_parent: 📚Learned
permalink: docs/Learned/HTTP/Cache
---

# 캐시와 검증헤더
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

## 캐시의 역할

<br>

캐시가 없을 때, 서버에 특정 데이터를 요청하면(GET Method), 그 데이터에 해당하는 용량만큼 계속 전송한다.

즉, 요청할 때마다 같은 데이터 크기 만큼을 계속 전송하게된다.

따라서,

1. 데이터가 변경되지 않아도, 계속 네트워크를 통해서 데이터를 다운 받아야 한다.
2. 인터넷 네트워크는 매우 느려지고 비싸진다.
3. 브라우저 로딩 속도가 느려진다.



이러한 문제를 해결하기 위해서 존재하는 것이 캐시이다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221203230425844.png" alt="image-20221203230425844" style="zoom:67%;" />
</p>

캐시 적용시,

**헤더의 [Cache-Control : 캐시가 유효한 시간] 에 따라 설정한 시간만큼 캐시에 저장된 데이터는 유효해지고, 시간 내에 같은 데이터 요청 시, 캐시에 보관된 데이터를 사용하게 된다.**

> 1. Cache-Control: max-age : 캐시 유효 시간 (초 단위)
>
> 2. Cache-Control: no-cache: 데이터는 캐시는 가능하지만, 항상 서버에 검증하고 사용
>
> 3. Cache-Control: no-store: 데이터에 민감한 정보가 있으므로 저장하면 안됨
>
> 4. Cache-Control: must-revalidate: 캐시 만료후 최초 조회시 원 서버에 검증해야함, **원 서버**(프록시 서버 X) **접근 실패시 반드시 오류를 발생시킨다.**

덕분에, 캐시가 없었을 때의 단점들이 모두 극복된다.

-> 하지만, **설정한 캐시 유효시간이 지나면 다시 서버에게 응답받아야한다.** 이를 또 해결할 수 있는 메커니즘이 존재한다.

<br>





## 검증헤더

<br>

### Last-Modified

<br>

**[검증 헤더 Last-Modified: 마지막 수정 일자] 가 존재해서, 마지막에 수정된 시간을 기록한 뒤, 클라이언트에게 헤더로 보내준다.**

따라서 마지막 수정일이 포함된 데이터가 캐시에 보관된다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221203231154300.png" alt="image-20221203231154300" style="zoom:67%;" />
</p>

이후에, 클라이언트는 요청을 보낼때, 마지막 수정일을 담아서 요청한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221203231307775.png" alt="image-20221203231307775" style="zoom:67%;" />
</p>

요청받은 서버에서의 **마지막 수정일이 같을 때, 데이터의 변경이 없다고 판단을 내린 후 서버는 304 NOT MODIFIED 라는 응답과 함께 빈 바디부를 클라이언트에게 보내준다.** (수정일자가 다를 시, 200 OK 응답과 함께 모든 데이터를 전송하게 된다.)

결과적으로, 클라이언트는 용량이 적은 헤더 데이터만 전송하게 되므로 매우 실용적으로 동작한다.

단, 검증헤더 만으로는, 데이터의 변경을 보장할 수 없다.(데이터는 그대로인데, 수정일자만 변하는 경우가 있을 수 있기 때문)

그래서 사용하는 것이 **ETag 헤더** 이다.

<br>

### ETag

<br>

ETag는 일종의 버전을 명시하는 헤더라고 볼 수 있다.

캐시용 데이터에 임의의 태그를 달아두고, 변경되면 ETag 값을 바꾼다. 따라서 ETag가 달라졌을 때, 모든 데이터를 다시 전송하게 하는 시스템이다.

Last-Modified 헤더 처럼 클라이언트가 요청한 ETag와 서버의 ETag가 같을 경우, 304 NOT MODIFIED 응답을 보낸다.







