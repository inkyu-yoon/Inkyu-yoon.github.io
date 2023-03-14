---
layout: post
title: "· Docker Container에 exec sh -c command로 여러개의 명령 보내기"
nav_order: 8
parent : Docker
grand_parent: 📚Learned
permalink: docs/Learned/Docker/ExecCommand
---

## Docker Container에 exec sh -c command로 여러개의 명령 보내기

<br>

도커 교과서 예제 문제를 풀다가 마주한 문제이다.

```
diamol/ch02-hello-diamol-web 이미지를 실행시키고, htdocs 디렉토리 안에 있는 index.html의 웹페이지 내용을 바꿔라
```

먼저 이미지를 실행시키는 것은 간단했다.



```
docker run -d -p 8080:80 --name web1 diamol/ch02-hello-diamol-web
```

위 명령어로 도커 이미지를 호스트 포트 8080과 도커 컨테이너의 80 포트를 연결한 뒤, `web1` 이라는 이름으로 `-d` 옵션으로 컨테이너로 실행시켰다.

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230314171315135.png" alt="image-20230314171315135" style="zoom:80%;" />
</p>

따라서, `localhost:8080` 으로 접속했을 때 위와 같은 페이지를 확인할 수 있었다.

이제 위 내용은 컨테이너의 htdocs 라는 디렉토리 안에 있는 `index.html` 파일 내용이라고 한다.

컨테이너에 명령어를 전달하는 `exec` command를 활용해서 hrdocs 안에 어떤 파일이 있는지 확인해보려고 했다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230314171531607.png" alt="image-20230314171531607" style="zoom:80%;" />
</p>

확인해보니, `ls` 명령어를 전달해서 확인해보니 htdocs 디렉토리가 보였다.

이제 htdocs 내부를 살펴보려면 htdocs 로 이동한 뒤, `ls` 명령어를 실행하면 될 것 같은데 그러기 위해서는 htdocs 위치로 `cd` 로 이동한 뒤, `ls` 명령어를 사용해야한다.

이때 사용할 수 있는 command가 `sh -c`이다.

> 

위 command와 함께 전달하고 싶은 명령어를 큰 따음표로 묶고 `;`로 분리해서 전달하면 된다.



`pwd` 명령어로 확인해보니, 컨테이너의 루트 경로가 `/usr/local/apache2` 임을 파악했다.

따라서 내 상황에서는

```
docker exec web1 sh -c "cd /usr/local/apache2/htdocs ; ls"
```

위와 같이 명령어를 전달하면 된다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230314172007437.png" alt="image-20230314172007437" style="zoom:80%;" />
</p>

원하는 결과를 얻었다.

<br>

{: .highlight }
참고 : [https://docs.docker.com/engine/reference/commandline/exec/#extended-description](https://docs.docker.com/engine/reference/commandline/exec/#extended-description)
