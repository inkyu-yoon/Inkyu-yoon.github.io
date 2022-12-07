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

<br>

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
  이러한 이미지로 컨테이너를 생성하면, 파일 시스템가 격리된 시스템 자원 및 네트워크를 사용할 수 있는 독립된 공간이 생성되고, 이것이 **도커 컨테이너**이다.

  (💡 예시. 웹 서버 도커 이미지에서 여러개의 컨테이너를 생성하면, 생성된 컨테이너 개수만큼 웹 서버가 생성될 것이다.)


### 3. 도커 정상 작동을 위한 체크 사항

<br>

1. 호스트 운영체제가 최소 3.10 버전 이상인지 `uname -r` 로 확인
2. 64 비트 리눅스인지 확인
3. sudo 명령어 혹은 root 권한으로 설치하기

<br>

## 도커 명령어 정리

<br>

### 컨테이너 생성 후 들어가기

```dockerfile
docker run -i -t 이미지:버전

//컨테이너 이름 부여하기
docker run -i -t --name 원하는이름 이미지:버전

//예시
docker run -i -t --name MyUbuntu ubuntu:14.04
```

<br>

### 컨테이너 생성하기 (실행 X , 내부로 들어가는 것 X )

```dockerfile
docker create -i -t 이미지:버전
```

<br>


### 컨테이너 create시 컨테이너 실행 및 내부로 들어가기

```dockerfile
//컨테이너 실행
docker start 컨테이너ID(혹은 이름)

//컨테이너 입장
docker attach 컨테이너ID(혹은 이름)

```

<br>


### 컨테이너 나가기

```dockerfile
//컨테이너 종료하면서 나가기
exit

//컨테이너 종료 없이 나가기
Ctrl + P + Q
```

<br>


### 컨테이너 목록 확인

```dockerfile
docker ps

//정지된 컨테이너까지 출력하고 싶은 경우
docker ps -a

//컨테이너 목록 깔끔하게 보고 싶은 경우
docker ps --format "table {{.ID}}\t{{.Status}}\t{{.Image}}\t{{.Names}}"

//컨테이너 이름 변경
docker rename 원래이름 변경이름
```

<br>


### 컨테이너 삭제

```dockerfile
// 컨테이너 정지 후 삭제
docker stop 컨테이너ID(혹은 이름)
docker rm 컨테이너ID(혹은 이름)

//컨테이너 강제 삭제
docker rm -f 컨테이너ID(혹은 이름)

//모든 컨테이너 삭제
docker container prune

//도커 ID 리스트화 후 삭제
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

<br>


### 컨테이너 외부 노출

{: .important }
> 아무런 설정을 하지 않으면, **외부에서 접근할 수 없으며 도커가 설치된 호스트에서만 접근할 수 있다.**
> 도커의 `NAT IP` 인 `eth0`와 포트를 호스트의 IP와 포트에 바인딩해야한다.



```dockerfile
docker run -i -t --name myserver -p <호스트의 포트>:<컨테이너의 포트> 이미지
```

> 위와 같은 형식으로 `-p` 옵션을 통해, 호스트의 포트와 컨테이너의 포트를 연결할 수 있다.
>
> 만약 호스트의 7777번 포트와 컨테이너의 80번 포트와 연결하고 싶다면 `-p 7777:80` 으로 사용하면 된다.
>
> 💡 컨테이너 내부에서 지원하는 포트인지 잘 확인하고 지원하는 컨테이너 포트와 연결하는 것이 중요하다.

<br>


## 애플리케이션 구축 예제

```dockerfile
docker run -d \
--name wordpressdb \
-e MYSQL_ROOT_PASSWORD = password \
-e MYSQL_DATABASE = wordpress \
mysql:5.7

// mysql 5.7버전 이미지를 컨테이너 이름을 wordpressdb 로서 실행했고, 환경변수를 전달했다.
// 참고로, 패스워드는 도커 스웜모드를 이용해서 안전하게 전달하는 것이 좋다.
```

>  [**-d**] 옵션과 [**-i -t**] 옵션의 차이
>
> - -d 옵션 : 컨테이너 내부에서 사용자의 입력을 받지 않고, 컨테이너를 백그라운드에서 동작하는 애플리케이션으로서 실행하도록 설정하는 것이다.
> - -i -t 옵션 : 사용자와 상호작용이 가능한 컨테이너 환경을 만들때 쓰인다.
>
> 💡 **-e** 옵션
>
> - 컨테이너 내부의 환경변수를 설정하는 것이다.

<br>

```dockerfile
docker run -d \
-e WORDPRESS_DB_HOST = mysql \
-e WORDPRESS_DB_USER = root \
-e WORDPRESS_DB_PASSWORD = password \
--name wordpress \
--link wordpressdb:mysql \
-p 80 \
wordpress

//참고로 --link 기능은 deprecated된 옵션이고 도커 브리지 네트워크를 사용하는 것이 좋다.
```

> 마찬가지로 환경변수를 전달했고, wordpress 이미지를 wordpress로 컨테이너 이름을 설정했다.
>
> -p 80 을 했으므로 **docker ps** 로 호스트의 어떤 포트와 연결되어 있는지 확인한다.


<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221207140608148.png" alt="image-20221207140608148" style="zoom:150%;" />
</p>

> 49153 포트와 컨테이너의 80 포트가 연결되었다.
>
> ec2를 사용한다면 인바운드 규칙을 편집해서 49153 포트를 추가하고,
>
> `ec2주소:49153` 으로 접속하면 어플리케이션을 확인할 수 있다.
---

<br>

## 도커 볼륨

<br>

- 컨테이너를 삭제하면 컨테이너에 존재하던 파일 시스템이나 애플리케이션 파일 뿐만 아니라, 그동안 쌓아놨던 로그인 정보나 게시글 등과 같은 데이터가 함께 삭제될 수 있다. 이를 방지하기 위해 사용하는 것이 **볼륨**이다.
- **볼륨을 활용하면, 데이터베이스 컨테이너를 삭제해도 데이터는 삭제되지 않도록 할 수 있다.**

```dockerfile
docker run -d \
--name wordpressdb_hostvolume \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
-v /home/wordpress_db:/var/lib/mysql \
mysql:5.7
```

```dockerfile
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

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103194714191.png" alt="image-20221103194714191" style="zoom:67%;" />
</p>

`/home/wordpress_db` 로 이동해보니, mysql과 관련된 파일들이 있는 것을 확인할 수 있다.

한번, 컨테이너를 삭제하고도 남아있는지 확인해보겠다.


<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221103194921016.png" alt="image-20221103194921016" style="zoom: 67%;" />
</p>

`docker rm -f wordpressdb_hostvolume`을 했음에도 여전히 파일이 남아있는 것을 확인할 수 있다.

{: .highlight }
❗ **이 상황에서 또 다른 컨테이너의 디렉터리와 `/home/wordpress_db`와 연결하면, 새로 연결된 디렉터리의 파일들로 완전히 덮어 씌워지므로 조심해야한다.**

