---
layout: default
title: "· 릴레이션의 분석 지침"
nav_order: 4
parent : DataBase
grand_parent: 📚Learned
permalink: docs/Learned/DataBase/RelationRule
---

# 릴레이션의 분석 지침
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

- 릴레이션이 잘 설계되었는지, 문제는 없는지 간단히 확인하는 기본 지침
- 기본 지침을 체크해보면, 잘 설계되었는지 대략적으로 확인할 수 있다.

<br>

## 1. 스키마에서 속성의 의미가 명확한지 확인한다.

<br>

- 속성의 이름을 의미적으로 **모호하지 않게** 작명해야 합니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020204206141.png" alt="image-20221020204206141" style="zoom: 80%;" />
</p>

-> 회원 테이블의 경우, `날짜 시간` 이라고 작명하기 보다는, 더 명확하게 `가입 날짜 시간` 이라고 작명해준다.

-> 게시글 데이터를 저장하기 위해 만든 게시글 릴레이션에는 게시글과 관련된 속성만 존재하게 한다. 위 예시처럼, `게시글 번호` 를 기본키로 하는 게시글 릴레이션에 `회원번호` `작성자 이름` `작성자 생년월일`  등과 같은 게시글과 관계가 먼 속성들이 존재하여 데이터가 중복되는 부분들이 많이 생기면 메모리 문제, 중복 문제 뿐만 아니라 다른 문제도 야기할 수 있다.



## 2. 튜플들에서 중복되는 값들이 있는지 확인한다.

<br>

### 1. 삽입 이상

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020204711064.png" alt="image-20221020204711064" style="zoom:80%;" />
</p>

실수로 작성자의 이름을 `김수민`으로 잘 못 입력될 수 있다. 나중에, 릴레이션의 튜플들을 확인했을 때, 회원 번호 1번이 `김민수` 인지 `김수민` 인지 확실하게 알 수 없어 릴레이션의 신뢰성을 잃게됩니다.

<br>

### 2. 수정 이상

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020204918115.png" alt="image-20221020204918115" style="zoom:80%;" />
</p>

회원 번호 1번인 회원의 이름을 특정한 이유로 일부만 변경되었을 때(실수로) , `삽입 이상` 때와 같은 이유로 릴레이션의 신뢰성을 잃게된다.

<br>

### 3. 삭제 이상

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020205118889.png" alt="image-20221020205118889" style="zoom:80%;" />
</p>

3번 게시글 튜플을 삭제 시, 게시글과 관련된 `제목` , `내용` 과 같은 데이터는 이유가 있어 삭제했다고 해도, 회원과 관련된 데이터도 모두 사라지게 된다. 이는, 데이터 누락을 야기하게 된다.

<br>

## 3. 튜플들에 저장된 NULL 값을 확인한다.

<br>

### 속성에 NULL 값이 빈번하게 저장되면, 불필요한 저장 공간을 사용하게 된다.

- -> 속성에 빈번하게 NULL 값이 저장된다는 의미가, 불필요한 정보라는 의미로 받아들일 수 있다. 따라서, 메모리 관점에서 이러한 속성이 해당 릴레이션에서 필수적인 속성인지 검토해볼 필요가 있다.

<p align="center">
  <img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020205537016.png" alt="image-20221020205537016" style="zoom:80%;" />
</p>

- NULL 값이 존재하면, 튜플의 의미가 모호해지거나 통계적 관점에서도 쓸모없는 데이터가 되어버리기 때문에, 별도의 릴레이션으로 분리하는 것을 고려하는 것이 좋다.

<br>

## 4. 가짜 튜플이 생성되는지 확인한다.

---

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020205937950.png" alt="image-20221020205937950"  />
</p>


위와 같은 경우가 릴레이션을 잘못 분리한 예시이다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221020210413774.png" alt="image-20221020210413774" style="zoom: 67%;" />
</p>


릴레이션을 분리했다가 다시 합쳤을 뿐인데, `(0003,데이터베이스,A0003)` 과 같은 이전에는 존재하지 않았던 튜플이 생성되었고, 이를 `가짜 튜플` 이라고 한다.

이처럼, 릴레이션을 적절하게 분리하지 않거나, 잘못된 설계를 하면, 두 릴레이션을 `JOIN` 할 시 `가짜 튜플` 을 만들어 낸다.



<br>


{: .note-title }
> 결론
>
> 1. 릴레이션의 속성이 명확하게 작명되었는지 확인한다.
>
> 2. 릴레이션의 데이터가 중복되는 부분이 많은지 확인한다.
> 
> 3. 속성 데이터가 NULL 값이 빈번하게 존재한다면, 필요한 속성인지 검토한다.
> 
> 4. 릴레이션 분리 시, 적절하게 분리되어 가짜 튜플을 생성하지 않는지 확인해본다.