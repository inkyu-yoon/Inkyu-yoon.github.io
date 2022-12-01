---
layout: post
title: "· 【IntelliJ】 콘솔 창 한글 깨짐 에러"
nav_order: 7
parent : Error
grand_parent: 📚Learned
permalink: docs/Learned/Error/KoreanError
---


# 【IntelliJ】 콘솔 창 한글 깨짐 에러

<br>

혼자 Querydsl 실습 중, 데이터베이스에 등록되어 있는 한글 데이터를 출력했는데, 한글이 알아볼 수 없는 글자로 출력이 되어 해결방법을 찾아봤다.

<br>


## Process 1

<br>

`Settings` 에 들어가서 `encoding` 을 검색하면 `File Encodings` 가 나온다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221202023800896.png" alt="image-20221202023800896" style="zoom:80%;" />
</p>


그다음 Project Encoding 을 `UTF-8`로 변경하고

아래 Properties Files 부분도 `UTF-8` 로 변경 뒤 체크 박스에 체크한다.





## Process 2

<br>

인텔리 제이 화면에서 `shift` 를 두번 누른 뒤,


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221202023526299.png" alt="image-20221202023526299" style="zoom:80%;" />
</p>


`edit custom VM Options` 를 검색한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221202023618608.png" alt="image-20221202023618608" style="zoom:80%;" />
</p>

```
-Dfile.encoding=UTF-8
```

위 구문을 추가한다.



콘솔 창에 한글이 정상적으로 나오는 것을 확인할 수 있다.