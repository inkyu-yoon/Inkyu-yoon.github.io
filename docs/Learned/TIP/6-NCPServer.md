---
layout: post
title: "·  NCP 서버 생성 후 접속하기"
nav_order: 6
parent : TIP
grand_parent: 📚Learned
permalink: docs/Learned/TIP/NCPServer
---

# NCP 서버 생성 후 접속하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


[https://www.ncloud.com/main/creditEvent](https://www.ncloud.com/main/creditEvent)

결제 수단을 등록하면 100000원 크레딧을 주기도 하고, 지금까지 AWS EC2 만 사용해봐서 NCP를 사용해보기로 했다!

💡참고로 크레딧은 결제 수단을 등록하면 자동으로 충전되는 것이 아니라, 위 페이지에서 별도로 신청 버튼까지 눌러야 한다.

<br>

## NCP 서버 생성하기

### 서버 생성


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427000850668.png" alt="image-20230427000850668" style="zoom:80%;" />
</p>

서비스 탭의 Server를 클릭한다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427000952292.png" alt="image-20230427000952292" style="zoom:67%;" />
</p>

이용 신청하기를 클릭한다.



<br>


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427001146237.png" alt="image-20230427001146237" style="zoom:80%;" />
</p>

서버 생성을 클릭한다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427001300282.png" alt="image-20230427001300282" style="zoom:80%;" />
</p>

Ubuntu-18.04 Compact 서버 타입으로 설정할 것이다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427001513520.png" alt="image-20230427001513520"  />
</p>

선택된 옵션과 서버 이름을 설정한다.



<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427001606051.png" alt="image-20230427001606051" style="zoom:80%;" />
</p>

인증키가 없다면 생성하면 된다.

난 이전에 생성해둔게 있으므로, 생성해둔 것을 사용하겠다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427001813087.png" alt="image-20230427001813087" style="zoom:80%;" />
</p>

mysql을 도커 컨테이너로 실행한 뒤, 3306 포트로 연결할 예정이라 ACG를 추가해주었다.

ACG 생성 후, 서버를 생성하면 된다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427002110278.png" alt="image-20230427002110278" style="zoom:80%;" />
</p>

서버 생성을 하면 위와 같은 창이 뜬다.

시작하는데 몇분이 소요되므로 시작될 때 까지 기다린다.

<br>

### 포트 포워딩 설정

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427002846955.png" alt="image-20230427002846955" style="zoom:67%;" />
</p>

서버 상태가 운영중으로 바뀌면 포트 포워딩 설정을 할 수 있다.

포트 포워딩을 설정해야, 서버로 접속할 수 있다.

<br>


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427005313364.png" alt="image-20230427005313364" style="zoom: 67%;" />
</p>

외부 포트의 값은 1024 ~ 65534 범위 내로 입력해야 한다.

나는 1028로 입력한 뒤 추가했다.

***위에서 생성된 서버 접속용 공인 IP로 서버에 접속할 수 있다.***

<br>

### ACG 설정


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427005555431.png" alt="image-20230427005555431" style="zoom:70%;" />
</p>

서버 접속을 하려면, ACG에 포트 포워딩으로 설정한 외부 포트를 추가해주어야 한다.

<br>


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427013422745.png" alt="image-20230427013422745" style="zoom:80%;" />
</p>

위와 같이, 포트 포워딩으로 설정했던 외부 포트를 ACG로 추가해주면 된다.

그리고 22 포트도 추가해놓는다.

<br>

### 관리자 비밀번호 확인



<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427003316777.png" alt="image-20230427003316777" style="zoom:67%;" />
</p>

서버 관리 및 설정 변경 탭에서, 관리자 비밀번호 확인을 클릭한다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427003417084.png" alt="image-20230427003417084" style="zoom:67%;" />
</p>

서버 생성 시, 생성했던 인증키 파일을 첨부하고 비밀번호 확인을 누른다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427003532794.png" alt="image-20230427003532794" style="zoom:80%;" />
</p>

비밀번호가 확인되면 관리자 이름과 비밀번호를 알려준다.

서버에 접속할 때, 필요하므로 따로 기록해둔다.

<br>





## Git Bash 로 서버 접속

```
ssh root@'서버 접속용 공인IP' -p '설정한 외부 포트'
```

위와 같이 입력하면 비밀번호를 입력하라고 하고, 비밀번호는 관리자 비밀번호를 입력하면 된다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427013719529.png" alt="image-20230427013719529" style="zoom: 67%;" />
</p>

참고로, 비밀번호를 입력할때는 보안 상 화면에 입력되는게 보이지 않으므로 똑바로 입력하고 엔터치면 된다.

<br>

```
passwd root
```

위 명령어를 입력해서,  새로운 비밀번호를 설정하면 이후에는 설정한 비밀번호로 접속할 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427014030997.png" alt="image-20230427014030997" style="zoom:150%;" />
</p>

<br>