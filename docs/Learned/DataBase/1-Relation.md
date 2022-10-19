---
layout: default
title: "· 릴레이션 · 키의 특징과 종류"
nav_order: 1
parent : DataBase
grand_parent: 📚Learned
permalink: docs/Learned/DataBase/Relation
---

# 릴레이션의 구성 요소 · 키의 특징과 종류
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 릴레이션과 스키마

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019112254258.png" alt="image-20221019112254258" style="zoom: 67%;" />
</p>

위와 같은 `MEMBER` 테이블(릴레이션)이 있는 경우



`M_ID`, `M_MEMBER_ID` 등 열에 해당하는,



데이터의 의미를 나타내고 자료의 구조를 정의하는 것을 릴레이션 스키마라고 한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019112312574.png" alt="image-20221019112312574" style="zoom:80%;" />
</p>


`select M_ID, M_MEMBER_ID, M_NAME, M_GENDER from MEMBER;`



구문을 입력하면 `MEMBER` 테이블에서 특정 정보를 가져온다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019112323797.png" alt="image-20221019112323797" style="zoom: 80%;" />
</p>

`select * from MEMBER;`



구문을 입력하면 `MEMBER` 테이블의 모든 값을 가져온다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019112343259.png" alt="image-20221019112343259" style="zoom:80%;" />
</p>

M_MEMBER_ID 와 M_GENDER 값만 필요하다면 위와 같이 실행시키면 된다.



<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019112359996.png" alt="image-20221019112359996" style="zoom: 67%;" />
</p>

위에서도 설명했듯이 회원번호, 아이디, 이름, 나이, 성별과 같이 자료의 구조를 정의하는 것을 **스키마**라고 하며



행에 해당하는 부분은 **튜플**, 열에 해당하는 부분은 **속성**이라고 한다.



**참고로 튜플의 경우 테이블을 보면 순서가 존재하는 것 처럼 보이지만, 순서가 따로 존재하지 않고 테이블은 단순히 튜플들의 집합일 뿐이다.**



하나의 데이터 테이블을 릴레이션이라고도 부르는데, 필요에 따라 다양한 형태로 릴레이션들이 만들어지고, 릴레이션끼리 연관이 없는 독립된 형태도 있겠지만, 두 릴레이션이 같은 스키마 원소를 공유하고 있는 경우, 두 릴레이션간에 관계를 갖게 된다.




<br>

테이블의 행에 해당하는 부분을 **튜플**이라고 했다.



릴레이션은 여러개의 튜플들로 이루어져 있고, 이 튜플들을 우리는 식별할 수 있어야한다.



만약 스키마 안에 이름이 있고 이름을 식별 기준으로 삼는다면, 동명이인이 있을 경우 어떤 튜플인지 알 수 없다.



하지만, 중복될 일이 없는 회원 번호와 같은 속성을 기준으로 한다면 쉽게 식별해낼 수 있을 것이다.



이처럼 튜플간 구분이 명확하고 식별해 낼수 있게 해주는 것을 **키(Key)**라고 한다.



그리고 키의 종류에는 **Super key · Candidate Key · Primary Key · Alternate Key / Unique Key · Foreign Key** 가 있다.





------

## Key의 종류와 특징

<br>

### **1. Super Key ( 슈퍼키 )**


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019112414845.png" alt="image-20221019112414845" style="zoom: 50%;" />
</p>

슈퍼 키는 튜플들 간에 식별을 명확하게 할 수 있으며 릴레이션 안에는 슈퍼키가 하나 이상 존재해야한다.



키로서 이름, 나이, 성별 을 사용할 경우 같은 이름의, 같은 나이, 같은 성별을 가진 튜플이 존재할 수 있기 때문에, 튜플 간 식별을 할 수 없는 경우가 생긴다.



하지만 회원번호나 아이디 같은 경우는 중복될 일이 없고, 튜플들 간 식별을 명확히 할 수 있다.



슈퍼키는 꼭 하나의 속성으로 이루어져 있을 필요는 없고 {회원번호,이름} 과 같이 튜플들을 명확히 구분지어줄 수 있는 속성을 포함한다면 {회원번호, 이름} 도 슈퍼키가 될 수 있다.





### **2. Candidate Key ( 후보키 )**



후보키란, 튜플들을 구별할 수 있는 슈퍼키로 만들 수 있는 최소의 속성 집합을 후보키라고 한다.



위 릴레이션에선는 회원번호와 아이디가 후보키라고 볼 수 있다.




<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019112428662.png" alt="image-20221019112428662" style="zoom:50%;" />
</p>

위와 같은 테이블에서, 단일 속성만으로는 슈퍼키를 만들 수 없다.



따라서 두개 이상의 속성을 묶어서 슈퍼키로 사용해야하는데, {회원번호, 관계} 나 {회원번호, 이름} 이 최소의 속성을 사용해서 만들 수 있는 슈퍼키이며, 후보키라고 할 수 있다.





### **3. Primary Key ( 기본키 )**



기본키란 최소의 속성을 사용해서 만든 슈퍼키인 후보키 중 하나를 정한 것이 기본키이다.



후보키의 경우, 튜플들을 식별할 수 있는 기준이 될 수 있고, 여러개의 후보키 중 하나를 선택한 것이다.



현업에서는, 보통 회원번호를 기본키로 잡고, 기본키로 잡은 속성에는 밑줄을 그어 표시한다.







### **4. Alternate Key ( 대체키 ) / Unique Key ( 유일키 )**



대체키와 유일키는, 여러개의 후보키 들 중 기본키를 정하고, 남은 키들을 대체키라고 부른다.



즉, 후보키 집합에서 기본키를 제외한 것이 대체키라고 볼 수 있다.







### **5. Foreign Key ( 외래키 )**



릴레이션간 관계는 공통으로 갖는 속성을 통해서 관계가 이어지는데, 서로 참조할 수 있게 만드는 키를 외래키라고 한다.




<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019112452020.png" alt="image-20221019112452020" style="zoom:80%;" />
</p>


위와 같이 게시물 릴레이션과 회원 릴레이션은 회원번호라는 속성을 통해 게시물 릴레이션은 회원 릴레이션을 참조하고, 회원 릴레이션은 게시물 릴레이션에 참조되어 있다.



즉, 다른 릴레이션을 참조할 수 있게 만드는 키를 외래키 라고 부른다.