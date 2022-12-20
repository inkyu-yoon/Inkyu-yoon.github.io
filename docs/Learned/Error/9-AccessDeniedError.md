---
layout: post
title: "· pull 명령 시 denied: access forbidden 에러"
nav_order: 9
parent : Error
grand_parent: 📚Learned
permalink: docs/Learned/Error/AccessDeniedError
---

# pull 명령 시 denied: access forbidden 에러

<br>
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221220111608270.png" alt="image-20221220111608270" style="zoom:80%;" />
</p>

<br>

```
docker pull registry.gitlab.com/inkyu-yoon/mutsasns:latest
Error response from daemon: Head "https://registry.gitlab.com/v2/inkyu-yoon/mutsasns/manifests/latest": denied: access forbidden

```

pull 하는 과정에서 위와 같은 에러가 발생했다.

발생한 이유는, 내가 접근하려고 하는 registry가 접근 권한이 **private** 였고, 단순 위치만 제공해서는 pull 받을 수 없어서 에러가 발생한 것 같다.

따라서, 내 gitlab의 아이디와 패스워드를 넘겨주어야 한다.

<br>


```
docker login registry.gitlab.com/inkyu-yoon/mutsasns
```

내가 접근하려고 하는 registry를 위와 같이 `docker login` 명령어를 먼저 입력한다.

<br>


```
docker login registry.gitlab.com/inkyu-yoon/mutsasns
Username: inkyu-yoon
Password:
```

그러면 위와 같이, `username`과 `password` 를 입력하라고 한다.

gitlab 아이디와 비밀번호를 입력하면 되는데, password는 입력할 때 화면에 나타나지 않으므로, 입력후 엔터를 누르면 된다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221220111732259.png" alt="image-20221220111732259" style="zoom:80%;" />
</p>

<br>

위와 같이 pull이 정상적으로 작동하게 되었다.