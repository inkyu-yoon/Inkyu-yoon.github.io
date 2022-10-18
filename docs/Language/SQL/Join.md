---
layout: default
title: "JOIN(조인) 이란"
nav_order: 1
parent : SQL
grand_parent: Language
permalink: docs/Language/SQL/Join
---

#  JOIN(조인) 에 대해서 알아보자
<br>




## JOIN 문법



- 2개 또는 그 이상의 테이블(릴레이션)을 결합하여 의미있는 데이터를 만들 수 있도록 돕는 메커니즘이다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017225123795.png" alt="image-20221017225123795" style="zoom: 67%;"  />
</p>

위와 같이, `M_ID` 를 외래키로서 공유하여 연관되어 있는 두 테이블이 있다고 할 때,



`Board` 테이블 만으로는, 게시글 작성자의 회원아이디·이름·성별·생일 에 대한 데이터를 알 수 없다.



하지만, `JOIN` 을 사용하면 알아낼 수 있다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017225421368.png" alt="image-20221017225421368" style="zoom: 80%;" />
</p>


`JOIN` 을 사용하면 두 테이블이 합쳐진 형태가 된다고 생각하면 된다.



---



## JOIN의 여러가지 방법



### 1. INNER JOIN


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017225609889.png" alt="image-20221017225609889" style="zoom:80%;" />
</p>

`SELECT * FROM A INNER JOIN B ON A_B_ID = B_ID;` 를 입력하면 `INNER JOIN` 이 된다.

`INNER JOIN` 은 두 릴레이션의 교집합을 추출한다고 생각하면 쉽다.

릴레이션 A의 A_B_ID 속성과 B의 B_ID 속성이 외래키로서 연결되어 있을 때, 두 테이블에서 데이터 값이 존재하는 튜플만 연결하여 추출해준다.



### 2. LEFT JOIN

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017225957135.png" alt="image-20221017225957135" style="zoom:80%;" />
</p>


`SELECT * FROM A LEFT JOIN B ON A_B_ID = B_ID;` 를 입력하면 `LEFT JOIN` 이 된다.



`LEFT JOIN` 은 A의 외래키 속성은 `NULL` 값까지 다 표현하고, `NULL` 값이 아닌 값들은 `B` 테이블의 데이터를 연관지어 표현한다.



당연히, A 테이블에서 `NULL` 값이었던 튜플들의 다른 속성 값도 `NULL` 값으로 표현된다.



### 3. RIGHT JOIN

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017230358418.png" alt="image-20221017230358418" style="zoom:80%;" />
</p>

`SELECT * FROM A RIGHT JOIN B ON A_B_ID = B_ID;` 를 입력하면, 



LEFT와는 반대로 B 테이블의 외래키 속성을 먼저 다 표현하고, A 테이블과 연관된 데이터들을 표현한다.



사실상 위 구문은 `SELECT * FROM B LEFT JOIN A ON A_B_ID = B_ID` 와 같은 표현이다.



**💡TIP?? : 모든 데이터를 표현하고 싶은 테이블을 `LEFT` 의 경우 왼쪽에 `RIGHT`의 경우 오른쪽에 배치함**

`SELECT * FROM [모든 튜플 표현하고 싶은 테이블1] LEFT JOIN  ON [테이블1 외래키] = [테이블2 외래키];`

`SELECT * FROM [연관된 테이블1] RIGHT JOIN [모든 튜플 표현하고 싶은 테이블2] ON [테이블1 외래키] = [테이블2 외래키];`



`LEFT` 나 `RIGHT` 중 직관적으로 이해가 잘되는 구문 하나를 정확히 이해하자.



---



### 4. FULL OUTER JOIN

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017231153486.png" alt="image-20221017231153486" style="zoom:80%;" />
</p>

`FULL OUTER JOIN` 의 경우 모든 데이터를 합쳐서 표현해준다. 



`LEFT JOIN` 과 `RIGHT JOIN` 이 중복 제거된 상태로 합쳐진 모습과 같다.



### 5. A-B

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017231346636.png" alt="image-20221017231346636" style="zoom:80%;" />
</p>

`A-B` 는 쉽게 생각하면, 먼저 `A`의 데이터를 전부 표현한 다음에, `B`와 겹치는 데이터를 삭제한 결과라고 생각하면 된다.

<p align="center">
![image-20221017231545205](https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017231545205.png)
</p>

따라서 `SELECT * FROM A LEFT JOIN B ON A_B_ID = B_ID` 를 사용하면 위와 같이 `A`의 데이터는 전부 표현될 것이다.



여기서 `B_ID IS NULL` 값인 조건을 추가해주면, 원하는 `A-B` 를 얻을 수 있다. (`A_B_ID IS NULL` 조건으로 추가해도 됨)



### 6. (A-B) U (B-A)

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017232537252.png" alt="image-20221017232537252" style="zoom:80%;" />
</p>

직관적으로 이해해보자면, 각 테이블을 `FULL OUTER JOIN` 해준후, 데이터가 `NULL` 값이 없는 온전한 튜플들을 제외시킨 테이블이다.

<p align="center">
![image-20221017233611722](https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017233611722.png)
</p>

뒤에 `WHERE A_ID IS NULL OR B_ID IS NULL;` 조건을 `WHERE A_B_ID IS NULL OR B_ID IS NULL` 로 해도 동일한 결과 일 것이다.



두 테이블의 외래키가 NULL 인 조건으로 생각하는게 쉬울 것 같다.



---



## 테이블 별칭 지정


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017234227496.png" alt="image-20221017234227496" style="zoom: 50%;" />
</p>


위와 같이 두개의 테이블이 외래키 `ID`로 연관되어 있을 때, `조인한 후 A의 ID 가 NULL 인 데이터`를 얻고 싶을 때,



위에서 배웠던대로 적용해보려고 하면,

{% capture some_var %}
```SQL
SELECT * FROM A LEFT JOIN B ON ID = ID
WHERE ID = NULL;
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

두 속성의 이름이 같아서, 쿼리문이 제대로 실행되지 않는다. 

{% capture some_var %}
```SQL
SELECT * FROM A LEFT JOIN B ON A.ID = B.ID
WHERE A.ID = NULL;
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


위와 같이 `.` 을 사용해서 구분해주거나


{% capture some_var %}
```SQL
SELECT * FROM A T1 LEFT JOIN B T2 ON T1.ID = T2.ID
WHERE T1.ID = NULL;
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


테이블 명이 길어서 입력하기 귀찮(?)거나 가독성이 떨어지는 경우 테이블 명을 지정해서 사용할 수도 있다.



