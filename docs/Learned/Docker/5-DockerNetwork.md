---
layout: post
title: "· 도커 네트워크 & Bridge"
nav_order: 5
parent : Docker
grand_parent: 📚Learned
permalink: docs/Learned/Docker/DockerNetwork
---

# 도커 네트워크 & Bridge
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



<br>

## 컨테이너 네트워크 확인

```
apt install net-tools
```

> 위 구문을 입력하여 라이브러리를 먼저 다운받는다.



<br>

```
ifconfig
```

> 위 구문을 입력하면, 도커의 네트워크 내부 IP 주소를 알 수 있다.



```
10:02:19 root@ip-172-31-36-87 ~ → ifconfig
docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:62ff:fe2e:7a8a  prefixlen 64  scopeid 0x20<link>
        ether 02:42:62:2e:7a:8a  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5  bytes 526 (526.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.31.36.87  netmask 255.255.240.0  broadcast 172.31.47.255
        inet6 fe80::443:6cff:fe46:1d13  prefixlen 64  scopeid 0x20<link>
        ether 06:43:6c:46:1d:13  txqueuelen 1000  (Ethernet)
        RX packets 206712  bytes 302916763 (302.9 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 19051  bytes 1672923 (1.6 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 318  bytes 37614 (37.6 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 318  bytes 37614 (37.6 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth9196c38: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::404d:b3ff:fe66:98b4  prefixlen 64  scopeid 0x20<link>
        ether 42:4d:b3:66:98:b4  txqueuelen 0  (Ethernet)
```

> mysql 이미지 컨테이너를 실행시킨 뒤, `ifconfig` 를 입력하니, 위와 같이 나타났다.
>
> `docker0`, `eth0`, `lo`, `veth9196c38` 를 브리지 라고 한다.

<br>

```
veth9196c38: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::404d:b3ff:fe66:98b4  prefixlen 64  scopeid 0x20<link>
        ether 42:4d:b3:66:98:b4  txqueuelen 0  (Ethernet)
```

> 컨테이너 마다 **veth...** 라는 네트워크 인터페이스를 생성한다. (veth : virtual eth)
>
> 따라서, 생성된 컨테이너의 갯수만큼 veth로 시작하는 인터페이스가 생성되어 있을 것이다.
>
> 그리고 이 **veth...** 와 도커의 **docker0** 네트워크가 연결되고, 도커의 **eth0**를 통해서 외부와 연결이 가능해지는 것이다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215193016661.png" alt="image-20221215193016661" style="zoom:80%;" />
</p>

> 위와 같은 구조라고 생각하면 된다.

<br>



## 도커 네트워크 기능

- 컨테이너를 생성하면 기본적으로 `docker0` 브리지를 통해 외부와 통신할 수 있는 환경을 사용할 수 있다.
- 도커가 자체적으로 제공하는 대표적인 네트워크 드라이버로는 **브리지(bridge), 호스트(host), 논(none)** 이 있다.

<br>

###  bridge(브리지) 네트워크 모드

<br>

```
docker network ls
```

> 위 구문을 입력하면, 기본적으로 사용할 수 있는 네트워크를 확인할 수 있다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215194938745.png" alt="image-20221215194938745" style="zoom:80%;" />
</p>

> 위와 같이 bridge, host, none 이 있는 것을 확인할 수 있다.
>
> 💡 host는 호스트 네트워크 환경을 그대로 사용할 수 있어 내부 애플리케이션을 별도로 포워딩할 필요가 없다.
>
> 💡 none은 아무런 네트워크를 사용하지 않는 것으로, 외부와 연결이 단절된다.

<br>


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215195301194.png" alt="image-20221215195301194" style="zoom:150%;" />
</p>

> 현재, mysqlDB라는 이름의 mysql 컨테이너가 실행중이다.

<br>

```
docker network inspect bridge
```

>  위 구문을 입력하면, bridge에 대한 자세한 정보를 확인할 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215195357027.png" alt="image-20221215195357027"  />
</p>

> `mysqlDB` 컨테이너가 자동으로 bridge를 활용하고 있음을 알 수 있다.

<br>

### 사용자 정의 bridge(브리지)

<br>

**기본적으로 존재하는 bridge 라는 이름의 브리지가 아닌, 사용자 정의 브리지를 생성해서 컨테이너와 연결시킬 수도 있다.**



```
 docker network create --driver bridge custombridge
 #  docker network create --driver 네트워크타입 네트워크이름
```

> 위 구문으로 네트워크를 생성할 수 있다.

<br>
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215195819073.png" alt="image-20221215195819073" style="zoom:80%;" />
</p>

> `custombridge` 라는 이름의 브리지가 생성되었다.

<br>

```
# 따로 브리지를 생성하지 않거나 컨테이너 실행 시 명시하지 않을 시 도커에서 기본으로 제공하는 브리지 연결
docker run --name mysqlDB2 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password -d mysql

# 생성한 브릿지에 --net 명령어로 연결
docker run --name mysqlDB2 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password --net custombridge -d mysql
```
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215203918752.png" alt="image-20221215203918752" style="zoom:80%;" />
</p>

> 직접 정의한 `custombridge` 에 mysql 컨테이너가 연결된 것을 볼 수 있다.

<br>

```
# 네트워크 연결 
docker network connect <네트워크모드> <컨테이너이름>

# 네트워크 연결 해제
docker network disconnect <네트워크모드> <컨테이너이름>
```

> 위 명령어로 네트워크 연결 및 해제를 할 수 있고, 이 명령어는 **논(none) 혹은 호스트(host)** 네트워크 모드에서는 **사용할 수 없다.**

<br>

### 다른 컨테이너의 네트워크 환경 공유

<br>

```
--net container:<다른 컨테이너 아이디>
```

> 위 명령어를 추가하면, 다른 컨테이너의 네트워크 환경을 공유할 수 있다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215210716333.png" alt="image-20221215210716333" style="zoom:80%;" />
</p>

> 위 그림과 같은 상황이 되는 것이다.
>
> 두 컨테이너 모두 바깥에서 같은 `veth0`로 접근할 수 있고, 내부에서는 localhost를 통해서 port로 통신할 수 있다.

<br>

{: .highlight }
참고 사이트 : https://coffeewhale.com/k8s/network/2019/04/19/k8s-network-01/



