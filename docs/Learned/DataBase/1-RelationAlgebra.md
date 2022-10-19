---
layout: default
title: "관계 대수(Relational Algebra) 란"
nav_order: 1
parent : DataBase
grand_parent: 📚Learned
permalink: docs/Learned/DataBase/RelationAlgebra
---

# 관계대수란?
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



## 관계 대수(Relational algebra)

- 관계 모델을 위한 기본적인 연산의 집합을 관계 대수라고 한다.
- 릴레이션을 피연산자 · 결과로 사용하는 연산자라고 생각하면 된다.
- 대표적으로 **순수 관계 연산자와 일반 집합 연산자** 가 있다.
- 과거에는 SQL이 없었기 때문에, SQL문 대신 관계 대수를 이용했고 지금도 DBMS의 내부언어로 사용되는 SQL의 이론적인 기초이다.



---



## 순수 관계 연산자

* 순수 관계 연산자는, 관계 데이터베이스에 적용하기 위해 개발한 연산자 이다.



### 1. SELECT

* `σ` 시그마 기호로 표현

* MYSQL 쿼리문에서 배운 것과 거의 동일함

  ```
  σ 조건식 (릴레이션 명)
  ```

  위와 같이 사용하고

<p align="center">
  <img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018002149349.png" alt="image-20221018002149349" style="zoom: 67%;" />
</p>

  예시로, 위와 같은 테이블이 있다고 가정하면

  ```
  σ (M_ID=1 OR M_ID=3) (MEMBER)
  ```

  이렇게 사용할 수 있다.

  

### 2. PROJECT

* `π` 파이 기호로 표현

* 특정 속성(칼럼)을 모두 표현한다.

  ```
  π (속성명) (릴레이션명)
  ```

  위와 같이 사용하고

  

  예시로

  ```
  π (M_ID,M_NAME) (MEMBER)
  ```

  이렇게 사용할 수 있다.

  

  참고로 데이터의 중복을 제거해서 추출한다.

  

### 3. EQUIJOIN(동등 조인)

* `⋈` 나비넥타이 기호로 표현

* SQL문의 JOIN과 비슷하다.

  ```
  (릴레이션명1) ⋈ <조인 조건> (릴레이션명2)
  ```

  

  예시로

{% capture some_var %}
  ```SQL
  SELECT * FROM MEMBER INNER JOIN BOARD 
  ON M_ID = BO_M_ID;
  ```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

  위와 같은 SQL의 JOIN문을

  ```
  (MEMBER) ⋈ M_ID=BO_M_ID (BOARD)
  ```

  이렇게 표현할 수 있다.

  

### 4. NATURAL JOIN(자연 조인)

* `*` 별 기호로 표현

* 조인 후 중복 속성을 제외한다.

  ```
  (릴레이션1) * <속성> (릴레이션2)
  ```

  위와 같이 사용하고

<p align="center">
  <img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018004754256.png" alt="image-20221018004754256" style="zoom:50%;" />
</p>
  

  예시로 위와 같은 경우는 `ID` 속성과 `B_ID` 속성이 결과 테이블에 존재하는 동등조인이다.

<p align="center">
  <img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018004906360.png" alt="image-20221018004906360" style="zoom:50%;" />
</p>

  자연 조인의 경우 데이터 값이 동일한 속성은 한개만 남긴다.

  

  ```
  (A) * (ID),(B_ID) (B)
  ```

  

  이렇게 사용할 수 있다.





### 6. DIVISION

* `÷` 로 표현한다.
* 예시로 파악하는게 제일 이해하기 쉽다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018005800970.png" alt="image-20221018005800970" style="zoom:67%;" />
</p>


```
A ÷ B 
```

디비전 연산을 하게되면, B의 등급 속성을 모두 갖고 있는 A의 종류 속성 데이터가 결과로 나온다.

---





## 일반 집합 연산자

* UNION (합집합) : 집합의 합집합과 동일

* INTERSECTION( 교집합)  : 집합의 교집합과 동일

* DIFFERENCE (차집합) : 집합의 차집합과 동일

* CARTESIAN PRODUCT (교차곱) : 모든 경우의 수를 표현함

  -> R = {A,B} , S={1,2,3} 이 있을 때, 카티션 곱(X)을 하면

  -> R X S = {A,1} {A,2} {A,3} {B,1} {B,2} {B,3} 이 된다.

  -> 데이터가 많을때 카티션 곱을 하면, 메모리가 감당 못할 수도 있음.. 