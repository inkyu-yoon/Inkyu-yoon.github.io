---
layout: post
title: "·  FileReader 시 한글 깨짐 해결"
nav_order: 1
parent : TIP
grand_parent: 📚Learned
permalink: docs/Learned/TIP/FileReader
---

# 텍스트 파일 FileReader 시 한글 깨짐 해결

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221111103305841.png" alt="image-20221111103305841" style="zoom:80%;" />
</p>

```java
BufferedReader br = new BufferedReader(new FileReader(filename));
```

<br>

`FileReader`를 사용해서 txt파일을 읽었는데, 깨짐 현상이 발생했다.

<br>



<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221111103324639.png" alt="image-20221111103324639" style="zoom:80%;" />
</p>

```java
  BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream(filename),"utf-8"));
```
<br>

`InputStreamReader` 와 `FileInputStream` 객체를 사용해서, **utf-8** 옵션을 주면, 해결된다!