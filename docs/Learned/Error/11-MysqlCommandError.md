---
layout: post
title: "· 도커로 실행한 MySQL, command not found 에러"
nav_order: 11
parent : Error
grand_parent: 📚Learned
permalink: docs/Learned/Error/MysqlCommandError
---



mysql:latest 이미지를 받아서 실행시킨 컨테이너를 bash 로 접속해서 vi 명령어를 사용하려고 하니 에러가 발생했고,

vi 를 사용하기 위해서는 apt-get 명령어를 써야한다고 했는데 역시 에러가 발생했고, sudo 명령어도 안되고 yum 명령어도 안되고..

모든 명령어가 동작을 안했다...

그래서 1시간 가량을 방법을 찾아봤고 겨우 성공했다..

```
bash: apt: command not found
bash: apt-get: command not found
bash: sudo: command not found
bash: su: command not found
... 다 에러 발생..
```



[https://stackoverflow.com/questions/73053173/apt-get-command-not-found-in-mysql-docker-cli](https://stackoverflow.com/questions/73053173/apt-get-command-not-found-in-mysql-docker-cli)

위 스택오버플로우 답변에 의하면

기본 패키지 관리자가 yum이라고 한다.. 근데 왜 yum 명령어도 에러가 났는지...

apt 명령어를 사용하고 싶다면 `mysql:8-debian` 이미지를 다운받으면 된다고 한다.

```bash
docker run --name 컨테이너이름 -e MYSQL_ROOT_PASSWORD=비밀번호 -d mysql:8-debian
```

위와 같이 버전을 명시해서 mysql을 실행하니 apt 명령어가 잘 동작했다.


<br>


```bash
docker exec -it 컨테이너이름 bash
apt-get update
apt-get install vim
```

위와 같이, 실행한 컨테이너에 bash 환경으로 접속한 뒤, vim 을 설치할 수 있게 되었다... ㅜㅜ(감격..)