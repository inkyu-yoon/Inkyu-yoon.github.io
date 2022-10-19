---
layout: default
title: "· 【IntelliJ】 Java file outside of source root 오류"
nav_order: 2
parent : Error
grand_parent: 📚Learned
permalink: docs/Learned/Error/OutOfError
---

# Java file outside of source root 오류

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019111328632.png" alt="image-20221019111328632" style="zoom:80%;" />
</p>


어이없었던 오류 해결..



IntelliJ로 Apllication 실행하려고 하는데, `Java file outside of source root` 문구가 나오면서



`Java application` 파일에 빨간 줄이 그어졌었다.



스택오버플로우에도 찾아보고 구글링도 해봤는데, 해결이 잘 되지 않았다.



하지만, 내가 실수했던 것이 있었는데,



`start.spring.io `에서 생성한 프로젝트 파일 폴더를 그냥 open만 했었다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019111345849.png" alt="image-20221019111345849" style="zoom:80%;" />
</p>

`file → open → build.gradle` 을 눌러서 open as project 로서 열고, build 하면 문제가 해결된다..