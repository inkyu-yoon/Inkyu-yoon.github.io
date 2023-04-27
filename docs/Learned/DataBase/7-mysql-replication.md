---
layout: post
title: "· MySQL Replication Master-Slave 이중화 시스템 구현하기"
nav_order: 7
parent : DataBase
grand_parent: 📚Learned
permalink: docs/Learned/DataBase/mysql-replication
---

#  MySQL Replication Master-Slave 이중화 시스템 구현하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


MySQL Replication은 마스터 서버에서 수신한 쿼리를 슬레이브 서버에 복제하는 방법으로, 마스터 서버에 쿼리를 실행하면 해당 쿼리가 슬레이브 서버에 자동으로 복제된다.

이렇게 복제된 데이터는 마스터 서버와 동일한 데이터를 가지며, 이중화된 데이터베이스 시스템을 구성할 수 있다.

이를 통해, 부하를 분산시킬 수 있으며 데이터 베이스 시스템의 가용성을 높일 수 있다.



## 적용 환경 및 초기 세팅



Master용 서버는, NCP 서버 [Compact] 1vCPU, 2GB Mem, 50GB [g1] / Ubuntu Server 18.04 (64-bit) 를 사용했고

Slave용 서버는 AWS ec2 서버  [t3 small] 2vCPU, 2GB Mem, 8GB / Ubuntu Server 22.04 LTS (64-bit) 를 사용했다.

<br>

```
docker run -p 3306:3306 --name master -e MYSQL_ROOT_PASSWORD=비밀번호 -d mysql:8.0.22
docker run -p 3306:3306 --name slave -e MYSQL_ROOT_PASSWORD=비밀번호 -d mysql:8.0.22
```

위 구문을 각 서버에 입력해서, mysql 컨테이너를 실행하면 된다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427022609938.png" alt="image-20230427022609938"  />
</p>

위와 같이 세팅했다.

<br>

## 적용하기



### Slave 서버 설정

```
 docker exec -it slave bash
```

위 명령어로 slave로 사용할 mysql 컨테이너로 접속한다.

<br>

```
mysql -u root -p
```

접속 후 위 명령어를 입력하고 컨테이너 생성시 설정했던 mysql 비밀번호를 입력한 뒤, mysql에 접속한다.

<br>

```
create database db명;
create user '계정이름'@'%' identified with mysql_native_password BY '비밀번호';
grant all privileges on *.* to '계정이름'@'%' with grant option;
flush privileges;
```

계정 이름과 비밀번호는 각자 원하는것을 입력하면 된다.

<br>

```
exit
```

위 명령어로 일단 mysql에서 나온다.

<br>

```
apt-get update
apt-get install vim
```

그리고 vim을 사용해야 하므로 위 구문을 입력한다.

<br>

```
cd etc/mysql
```

위 명령어로 위치를 이동하고 `ls` 를 해보면

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427023558567.png" alt="image-20230427023558567" style="zoom:150%;" />
</p>

위와 같이 `my.cnf` 파일이 있는 것을 확인할 수 있다.

저 파일을 수정해야한다.

<br>

```
vim my.cnf
```

위 명령어를 입력 후

<br>

```
log-bin=mysql-bin    
server-id=2        
```

위 구문을 아래와 같이 추가해준다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427030114233.png" alt="image-20230427030114233" style="zoom: 67%;" />
</p>

`:wq` 로 저장하고 빠져나오면 된다.

<br>

### master 서버 설정

```
 docker exec -it master bash
```

이제 master로 사용할 서버의 mysql 컨테이너로 접속한다.

<br>

```
apt-get update
apt-get install vim
```

마찬가지로, vim 명령어를 사용해야 하므로, 위 명령어로 일단 vim 을 설치한다.

<br>

```
cd etc/mysql
```

위 명령어로 위치를 이동하고

<br>

```
vim my.cnf
```

위 명령어를 입력 후

<br>

```
log-bin=mysql-bin    
server-id=1           
binlog-do-db=db명
```

위 내용을 아래와 같이 추가한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427024034823.png" alt="image-20230427024034823" style="zoom:80%;" />
</p>

나는 db명이 `shoekream` 이므로 위와 같이 설정했다.

`binlog-do-db` 설정은 지정한 db의 로그파일만 확인하기 위해 추가한 구문이다.



### master · slave 컨테이너 재시작

<br>



`ctrl + p` 와  `ctrl + q ` 를 입력해서 컨테이너에서 나온다.

```
docker restart master
docker restart slave
```

위 구문을 각 서버에 입력해서, master 서버와 slave 서버 둘 다 재시작한다.



### Master 서버 설정



```
docker exec -it master bash
mysql -u root -p
```

다시 master 서버의 mysql로 접속한다.

<br>

```
create database shoekream;
create user '계정이름'@'%' identified with mysql_native_password BY '비밀번호';
grant all privileges on *.* to '계정이름'@'%' with grant option;
flush privileges;
```

위 구문을 입력해서 마스터 서버에서도 권한을 부여한다.

<br>

```
show master status;
```

그리고 위 구문을 입력하면

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427201340184.png" alt="image-20230427201340184"  />
</p>

위와 같은 정보를 확인할 수 있다.

파일명과 포지션 번호는 기억해두어야 한다.

<br>

### Slave 서버 설정

```
CHANGE MASTER TO MASTER_HOST='master ip주소', MASTER_PORT=3306, MASTER_USER='계정명', MASTER_PASSWORD='비밀번호', MASTER_LOG_FILE='로그 파일명', MASTER_LOG_POS=포지션번호;
```

slave mysql 서버에 접속해서 위와 같은 형식의 명령어를 입력한다.

작은 따옴표도 포함해서 입력해야한다.

> MASTER_HOST : master mysql이 있는 서버 ip (ex. `MASTER_HOST='35.78.175.91'`)
>
> MASTER_PORT : master 서버의 mysql 포트
>
> MASTER_USER : master 서버에서 create user 한 뒤, 권한을 부여했던 계정명 (ex. `MASTER_USER='inq'`)
>
> MASTER_PASSWORD : master 서버에서 create user 한 뒤, 권한을 부여했던 계정의 비밀번호 (ex. `MASTER_USER='password'`)
>
> MASTER_LOG_FILE : master 서버에서 `show master status;` 했을 때 나온 file 명 (ex.`MASTER_LOG_FILE='mysql-bin.000001'`)
>
> MASTER_LOG_POS: master 서버에서 `show master status;` 했을 때 나온 Position (ex.`MASTER_LOG_POS='1039'`)

<br>

```
start slave;
SHOW SLAVE STATUS\G;
```

그리고 위 구문을 입력해서 확인해보면

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427201458704.png" alt="image-20230427201458704" style="zoom: 67%;" />
</p>

위와 같이 `Slave_IO_Running`와 `Slave_SQL_Running` 값이 `YES` 이면 정상 연결이 된 것이다.

<br>

### 테스트해보기

master 서버와 slave 서버에 `shoekream` 이라는 스키마를 만들어놨으므로, 테스트해서 반영되나 확인해보겠다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427201736338.png" alt="image-20230427201736338" style="zoom:130%;" />
</p>

<br>

```
use shoekream;
```

만들어둔 스키마로 들어간다.

<br>

```
CREATE TABLE test (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY);
```

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427202258140.png" alt="image-20230427202258140" style="zoom:120%;" />
</p>

마스터 서버에 위 구문을 입력한 뒤, slave 서버에서 `show tables;` 를 입력해보면,

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230427202335741.png" alt="image-20230427202335741" style="zoom:120%;" />
</p>

위와 같이 master 서버에서 생성한 테이블이 생긴 것을 확인할 수 있다.

