---
layout: default
title: "· 함수적 종속성과 정규화"
nav_order: 5
parent : DataBase
grand_parent: 📚Learned
permalink: docs/Learned/DataBase/NF
---

# 함수적 종속성과 정규화(1NF·2NF·3NF·BCNF)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


##  정규화란?

<br>

- 관계형 데이터베이스 설계에서 정보를 보존한 상태로 중복을 최소화하는 방향으로 데이터를 구조화하는 과정

- 정규화를 배우기 전에, 함수적 종속성(functional dependency)가 뭔지 알고 가자.

---

## 함수적 종속성

<br>

- X 와 Y 속성이 있을 때, **X가 Y의 값을 고유하게 결정**한다면 Y는 X에 함수적으로 종속된다고 부르고 `X → Y`로 표기한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020212024852.png" alt="image-20221020212024852" style="zoom:80%;" />
</p>

예를 들어 위와 같은 테이블이 있다고 했을 때,



회원번호 데이터 1개는 회원번호 데이터 1개와 연관된 나머지 속성들을 1개로 특정지을 수 있다.

아이디 데이터 역시 중복 생성이 불가한 경우, 아이디 데이터 1개와 연관된 다른 속성의 데이터를 특정지을 수 있다.



<br>



하지만, 이름 속성은, 이름 데이터 1개가 주어졌을 때, 그 이름 데이터와 연관된 다른 데이터를 특정지을 수 없다.

예를 들어, 위 릴레이션에서 회원번호 `5`번을 갖는 동명이인 `김민수` 가 추가된다면, `김민수` 데이터만 알고 있다고 했을 때, 회원번호가 `1` 인지 `5` 인지 고유하게 결정할 수 없다. 성별, 생년월일, 가입날짜 모두 마찬가지다.



**주의할 점이 있는데**, 어떤 속성과 속성이 우리가 알고 있는 데이터만으로는 함수적 종속성을 만족할지라도, 예외 경우가 존재할 수 있기 때문에, 함수적 종속성을 파악하기 어렵다. 하지만, 함수적 종속성 규칙을 깨는 경우는 확실하게 함수적 종속성을 만족하지 않음을 확정지을 수 있다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020212647677.png" alt="image-20221020212647677" style="zoom: 80%;" />
</p>

위 스키마를 보면, `X → Y` 관계에서 X역할을 할 수 있는 속성의 경우 {회원번호},{아이디},{회원번호,아이디} 임을 알 수 있다.



---





## 제 1 정규형

<br>

- 속성의 데이터 값은 하나의 값(원자 단위로)으로 존재해야 한다.
- 
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020213254544.png" alt="image-20221020213254544" style="zoom:80%;" />
</p>

위 릴레이션에서, 관심사 속성의 데이터는 1개의 데이터만 저장하고 있지 않기 때문에, **제 1 정규형을 위반한다.**

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020213425950.png" alt="image-20221020213425950" style="zoom:80%;" />
</p>

그렇다고, 위와 같은 방식으로 수직적으로 분리하면 안된다. 딱봐도, 릴레이션 분석지침 중 하나인, 중복되는 데이터들을 가진 튜플이 많이 보인다.



따라서, **수평적 분리**를 먼저 해주고, **수직적 분리**를 해주는 것이 좋다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020213707910.png" alt="image-20221020213707910" style="zoom:80%;" />
</p>


1. 먼저 기존 릴레이션의 기본키와 다치값을 가지는 속성(제 1정규형을 위반하는 속성)을 분리하여 새로운 릴레이션을 생성한다.
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020213722518.png" alt="image-20221020213722518" style="zoom:80%;" />
</p>

2. 수평적 분리를 먼저 진행한 후에, 수직적으로 분리해주고, 기본키를 `회원번호` 속성만으로는 역할을 할 수 없기 때문에, `{회원번호,관심이름}` 을 기본키로 설정해준다.



---





## 제 2 정규형

<br>

- 제 1 정규형을 만족하며, 기본키 속성이 **2개** 이상인 경우, **키가 아닌 속성이 기본키의 일부분에 함수적으로 종속되서는 안된다. **
  즉, 기본키 속성들 중 일부 속성으로, 함수적 종속성이 만족되는 속성은 릴레이션을 분리시키는 것이 중복제거하기도, 관리하기도, 데이터를 파악하기도 편하다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221024194702356.png" alt="image-20221024194702356" style="zoom: 67%;" />
</p>

위와 같은 테이블에서, 기본키는 **회원번호와 게시글번호** 이다. 테이블 속성중, **회원이름, 회원성별, 회원 생년월일** 은 기본키 중 하나인 **회원번호** 만으로도 구별이 가능하다. 즉, 기본키 속성 중 하나로, 함수적 종속성을 만족한다.  또한, **게시글 번호**로 **게시글 제목, 게시글 내용**울 구별할 수 있고, 함수적 종속성을 만족한다. 이런 관계를 갖는 속성은 릴레이션을 분리해서 관리하는 것이 좋고, 이런 과정을 제 2 정규형을 위배한다고 볼 수 있다.
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221024194952850.png" alt="image-20221024194952850" style="zoom:67%;" />
</p>

하나의 릴레이션을 위와 같이 3개의 릴레이션으로 분리할 수 있고, 이러한 과정을 제 2 정규화 라고 할 수 있다.



---





## 제 3 정규형

<br>

- 이행적 종속(transitive dependency) 관계가 릴레이션에서 존재하면, 제 3 정규형을 위배했다고 볼 수 있다.
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221024195720213.png" alt="image-20221024195720213" style="zoom: 67%;" />
</p>

위와 같은 댓글 릴레이션이 있다고 했을 때, **회원번호, 작성자 이름, 작성자 성별** 은 중복 값을 갖으므로 키의 속성이 될 수 없고, **게시글 번호, 게시글 제목** 또한 키의 속성이 될 수 없다. 하지만, **회원번호** 속성은 **작성자 이름, 작성자 성별** 과 함수적 종속 관계에 있고, **게시글 번호** 역시 **게시글 제목** 과 함수적 종속 관계에 있다.

직관적으로 현재 댓글 릴레이션만 봐도, 중복되는 데이터들이 눈에 많이 보이게 되는데, 위와 같은 경우에 릴레이션을 분리해주는 것을 제 3 정규화 라고 한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221024195956693.png" alt="image-20221024195956693" style="zoom:67%;" />
</p>


즉, **기본 키**가 아닌 모든 속성이 **기본 키**에 이행적 함수 종속이 되지 않으면 **제 3정규형** 이라고 볼 수 있다.



---



## BCNF(Boyce-Codd Normal Form)

<br>

- 제 3 정규형과 비슷하지만, 조금 더 엄격한 규칙이다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221024202055567.png" alt="image-20221024202055567" style="zoom:80%;" />
</p>

위와 같은 릴레이션이 있다고 할때, 제 3 정규형은 만족한다. 왜냐하면, **회원번호와 과목이름**이 키로서 존재할때, **강사번호**를 구별할 수 있고, 단일 속성으로는 튜플을 구별할 수 없기 때문이다. 따라서 이행적 함수 종속이 일어나는 속성은 없다. 하지만, **강사번호**는 **과목이름** 을 결정지을 수 있다. 즉, **함수적 종속 관계**에 있다.

이러한 경우, 릴레이션을 분리시켜주는 것이 **BCNF** 이다.



릴레이션을 분리하는 과정에서, 여러가지 경우의 수가 있겠지만, **강사 번호**는 **과목이름**을 결정할 수 있기 때문에, **강사 번호**를 기본키로 갖는 테이블은 데이터의 중복을 최소화 할 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221024202639273.png" alt="image-20221024202639273" style="zoom:80%;" />
</p>

따라서, 위와 같이 {강사번호} 를 기본키로 갖는 릴레이션 1개와, {강사번호,회원번호}를 기본키로 갖는 릴레이션 1개로 분리할 수 있다.