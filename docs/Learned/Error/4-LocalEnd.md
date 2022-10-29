---
layout: post
title: "· cmd를 이용해서 localhost 종료"
nav_order: 4
parent : Error
grand_parent: 📚Learned
permalink: docs/Learned/Error/LocalEnd
---

# cmd를 이용해서 localhost 종료하기

<br>

localhost:8080가 켜져있어서 에러가 발생하는데 어디에서 실행되고 있는지 모르는 경우, 끄는 방법을 살펴보겠다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221029153217092.png" alt="image-20221029153217092"  />
</p>

cmd 창을 키고



`netstat -ano` 를 입력한다.



꺼야 하는 localhost 가 8080이므로 로컬주소 8080을 찾고 PID(내 경우에는 17984)를 기억해둔다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221029153237811.png" alt="image-20221029153237811" style="zoom: 80%;" />
</p>

`taskkill /f /pid 17984` 를 입력하면 종료된다