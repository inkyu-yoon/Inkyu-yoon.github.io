---
layout: post
title: "· Docker 명령어와 볼륨"
nav_order: 3
parent : Docker
grand_parent: 📚Learned
permalink: docs/Learned/Docker/DockerBasic
---

# Docker 명령어와 볼륨
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



 

도커는 **리눅스 컨테이너**에 여러 기능을 추가함으로써 **애플리케이션을 컨테이너로서 조금 더 쉽게 사용**할 수 있게 만들어진 오픈소스 프로젝트이다.
<br>

---


## 도커를 사용하는 이유는?
<br>

- 서버를 부팅할 때 실행되는 운영체제를 일반적으로 '호스트 OS'라고 부르며, **도커 컨테이너는 호스트 OS 위에서 실행되는 격리된 공간이다.**
  따라서, 컨테이너 자체에 특별한 권한을 주지 않는 한, **컨테이너 내부에서 수많은 소프트웨어를 설치하고 설정파일을 수정**해도, **호스트 OS에는 영향을 끼치지 않는다.**


- 여러 모듈을 독립된 형태로 구성하는 **마이크로서비스 구조**라서 변화에 빠르게 대응할 수 있고, 각 모듈의 관리가 쉬워진다는 장점이 있다.
  (💡예시. 웹서비스는 데이터베이스 컨테이너와 웹 서버 컨테이너로 분리할 수 있을 것이다.)

<br>

[EC2 인스턴스 생성과 도커 설치 법](https://inkyu-yoon.github.io/docs/Learned/Docker/DockerAndEc2)

위 링크를 참고하면, ec2 서버에 docker를 설치할 수 있다.

---

## 도커 용어 정리

<br>

### 1. 도커 이미지

- 이미지는 **컨테이너를 생성할 때 필요한 요소**이다. 여러 개의 계층으로 된 바이너리 파일로 존재하고, 컨테이너를 생성하고 실행할 때 읽기 전용으로 사용된다.
- 이미지의 이름은 기본적으로 `[저장소 이름]/[이미지 이름]:태그` 의 형태로 구성된다.

  1️⃣ 저장소 이름 : 이미지가 저장된 장소, 따로 명시하지 않으면, **도커에서 기본적으로 제공하는 이미지**를 뜻한다.

  2️⃣ 이미지 이름 : 해당 이미지가 어떤 역할을 하는지 나타낸다. **이미지 이름은 생략할 수 없다.**

  3️⃣ 태그 : 이미지의 버전관리에 사용되며, 태그를 생략하면 이미지 태그를 **latest로 인식**한다.

### 2. 도커 컨테이너

- 도커 이미지는, 기본적인 운영체제부터, 데이터베이스, 각종 어플리케이션, 빅데이터 분석 도구 까지 다양한 종류가 있다.
  이러한 이미지로 컨테이너를 생성하면, 파일 시스템가 격리된 시스템 자원 및 네트워크를 사용할 수 있는 독립된 공간이 생성되고, 이것이**도커 컨테이너**이다.

  (💡 예시. 웹 서버 도커 이미지에서 여러개의 컨테이너를 생성하면, 생성된 컨테이너 개수만큼 웹 서버가 생성될 것이다.)

---

## 도커 실습해보기

<br>

### 도커 이미지를 이용한 컨테이너 생성

<br>

```
// run으로 이미지 생성 시, 생성 후 컨테이너 내부로 들어간다.
docker run -i -t ubuntu:22.04

// create로 이미지 생성 시, 컨테이너 내부로 바로 들어가지 않는다.
// 그래서 컨테이너 start(실행)후 attach(접속) 해야한다.
docker create -i -t ubuntu:22.04

```

를 입력해서 `ubuntu:22.04` 이미지를 도커 중앙 저장소인 도커 허브에서 내려받을 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103164650942.png" alt="image-20221103164650942" style="zoom: 67%;" />
</p>

다운이 완료되면, 컨테이너 내부로 들어올 수 있다.

`root@~~: /#` 로 바뀌면 컨테이너에 들어온 상태인 것이고, `exit` 를 입력하면 컨테이너에서 나올 수 있다.

단, `exit`로 컨테이너를 나오는 경우, 컨테이너를 정지하면서 나오기 때문에, `Ctrl + P, Q` 를 사용하면, 정지시키지 않고 나올 수 있다.



<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103170056960.png" alt="image-20221103170056960" style="zoom:67%;" />
</p>

이번에는 `create`로 이미지를 생성한 뒤, `docker ps`로 확인해보니, 실행되고 있지 않음을 확인할 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103170308693.png" alt="image-20221103170308693" style="zoom:67%;" />
</p>

`docker ps -a` 명령어로 컨테이너의 이름을 확인할 수 있었다. `silly_napier` 이므로, 해당 이름을 이용해서 `start`와 `attach`를 하면 된다.

컨테이너 생성 시에 이름을 직접 지정해줄 수도 있다.

```
docker create -i -t --name <원하는이름> 이미지
```

위 구문을 입력하면 된다. 이름을 `thisismine`으로 테스트 해보겠다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103170652797.png" alt="image-20221103170652797" style="zoom:67%;" />
</p>

원하는 이름으로 잘 지정되어 생성된 것을 확인할 수 있다.

<br>

### 도커 컨테이너 제거하기

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103171024275.png" alt="image-20221103171024275" style="zoom:80%;" />
</p>

현재 `silly_napier`라는 이름의 컨테이너가 실행 중인데, 이 컨테이너를 제거해보겠다.

```
//원하는 컨테이너 삭제
docker rm -f <컨테이너 이름>

//실행 중지된 컨테이너들을 한번에 다 삭제하고 싶은 경우
docker container prune
```

실행중인 컨테이너는 `stop` 명령어를 사용한 뒤 `rm` 명령어로 제거해도 되지만, `-f`를 붙이면 실행중인 컨테이너도 한번에 제거할 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103171321747.png" alt="image-20221103171321747" style="zoom:80%;" />
</p>

원하는 컨테이너를 삭제했다.



### 도커 포트 연결하기

<br>

```
docker run -i -t --name myserver -p <호스트의 포트>:<컨테이너의 포트> 이미지
```

위와 같은 형식으로 `-p` 옵션을 통해, 호스트의 포트와 컨테이너의 포트를 연결할 수 있다.

만약 호스트의 7777번 포트와 컨테이너의 80번 포트와 연결하고 싶다면 `-p 7777:80` 으로 사용하면 될 것이다.

💡 컨테이너 내부에서 지원하는 포트인지 잘 확인하고 연결하는 것이 중요하다.



### 데이터베이스와 워드프레스 웹 서버 연동해보기

```
docker run -d \
--name wordpressdb \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
mysql:5.7 
```

> 💡 [**-d**] 옵션과 [**-i -t**] 옵션의 차이
>
> - -d 옵션 : 컨테이너 내부에서 사용자의 입력을 받지 않고, 컨테이너를 백그라운드에서 동작하는 애플리케이션으로서 실행하도록 설정하는 것이다.
> - -i -t 옵션 : 사용자와 상호작용이 가능한 컨테이너 환경을 만들때 쓰인다.
>
> 💡 **-e** 옵션
>
> - 컨테이너 내부의 환경변수를 설정하는 것이다.

위 구문을 입력하여 mysql 이미지로 컨테이너를 생성 후 실행시키고



```
  docker run -d  \
  -e WORDPRESS_DB_HOST=mysql  \
  -e WORDPRESS_DB_USER=root  \
  -e WORDPRESS_DB_PASSWORD=password  \
  --name wordpress  \
  --link wordpressdb:mysql  \
  -p 80  \
  wordpress
```

> --link 옵션으로 mysql 컨테이너의 이름이 wordpressdb로 설정되어 있는데, 이를 mysql 이라는 별명으로 설정하여 ip를 몰라도 wordpressdb 컨테이너로 쉽게 접근할 수 있게 설정하는 것이다.



두개의 컨테이너를 설치했으면, 워드프레스로 접속해보겠다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103143200567.png" alt="image-20221103143200567" style="zoom: 150%;" />
</p>

`docker ps` 를 입력해서 wordpress 의 port 번호를 확인해보니 `49153` 임을 확인하였고, 컨테이너 포트는 80이다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103143629431.png" alt="image-20221103143629431" style="zoom:67%;" />
</p>

접속하기 위해서 ec2 인바운드 규칙에서 49153 포트를 추가한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103143657383.png" alt="image-20221103143657383" style="zoom: 50%;" />
</p>

`ec2주소:49153` 으로 접속하면 위와 같은 화면이 나올 것이다.

---

## 도커 볼륨

<br>

- 컨테이너를 삭제하면 컨테이너에 존재하던 파일 시스템이나 애플리케이션 파일 뿐만 아니라, 그동안 쌓아놨던 로그인 정보나 게시글 등과 같은 데이터가 함께 삭제될 수 있다. 이를 방지하기 위해 사용하는 것이 **볼륨**이다.
- **볼륨을 활용하면, 데이터베이스 컨테이너를 삭제해도 데이터는 삭제되지 않도록 할 수 있다.**

```
docker run -d \
--name wordpressdb_hostvolume \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
-v /home/wordpress_db:/var/lib/mysql \
mysql:5.7
```

```
  docker run -d \
  -e WORDPRESS_DB_PASSWORD=password  \
  --name wordpress_hostvolume  \
  --link wordpressdb_hostvolume:mysql \
  -p 80  \
  wordpress
```



`-v /home/wordpress_db:/var/lib/mysql` 구문을 통해서 호스트의 `/home/wordpress_db` 위치와 컨테이너의 `/var/lib/mysql`위치를 공유하게 되었다.

(💡 `/var/lib/mysql` 는 MYSQL이 데이터를 저장하는 기본 디렉터리이다.)

컨테이너의 디렉터리에 쌓이는 파일이 호스트의 디렉터리에도 쌓이게 되고, 컨테이너를 삭제하게 되어도 호스트의 디렉터리에 파일이 남아있게 된다.

컨테이너가 삭제되어도 `/home/wordpress_db` 디렉터리에 파일이 남아있게 되는 것이다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103194714191.png" alt="image-20221103194714191" style="zoom:67%;" />
</p>

`/home/wordpress_db` 로 이동해보니, mysql과 관련된 파일들이 있는 것을 확인할 수 있다.

한번, 컨테이너를 삭제하고도 남아있는지 확인해보겠다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103194921016.png" alt="image-20221103194921016" style="zoom: 67%;" />
</p>

`docker rm -f wordpressdb_hostvolume`을 했음에도 여전히 파일이 남아있는 것을 확인할 수 있다.

> ❗ **이 상황에서 또 다른 컨테이너의 디렉터리와 `/home/wordpress_db`와 연결하면, 새로 연결된 디렉터리의 파일들로 완전히 덮어 씌워지므로 조심해야한다.**

