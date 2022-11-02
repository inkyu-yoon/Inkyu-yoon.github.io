---
layout: post
title: "· Git Bash로 Docker 설치하기"
nav_order: 1
parent : 이것저것
grand_parent: 📚Learned
permalink: docs/Learned/OddsAndEnds/Docker
---

#  Git Bash로 Docker 설치하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



## AWS EC2 인스턴스 생성


<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184628317.png" alt="image-20221102184628317" style="zoom:80%;" />
</p>


먼저, `인스턴스 시작` 버튼을 눌러 인스턴스를 생성한다.


<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184642602.png" alt="image-20221102184642602" style="zoom: 67%;" />
</p>


이름은 원하는 이름으로 작성하면 되지만, t3.small 인스턴스를 생성할 것이기 때문에 t3.small로 했다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184655428.png" alt="image-20221102184655428" style="zoom:67%;" />
</p>


Ubuntu를 클릭하고 Ubuntu Server 22.04.LTS 를 선택한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184709174.png" alt="image-20221102184709174" style="zoom: 67%;" />
</p>


인스턴스 유형으로는 t3.small을 선택한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184721818.png" alt="image-20221102184721818" style="zoom:67%;" />
</p>


키 페어를 생성한다. `새 키패어 생성`을 클릭한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184740762.png" alt="image-20221102184740762" style="zoom: 67%;" />
</p>


키 페어 이름은 원하는 이름으로 짓고



키페어 유형은 RSA , 프라이빗 키 파일 형식은 .pem 으로 설정하고 `키 페어 생성`을 클릭한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184847788.png" alt="image-20221102184847788" style="zoom:67%;" />
</p>


네트워크 설정에서 두 항목을 체크한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184859470.png" alt="image-20221102184859470" style="zoom:80%;" />
</p>


스팟 인스턴스 요청을 체크한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184913409.png" alt="image-20221102184913409" style="zoom:67%;" />
</p>


`인스턴스 시작` 버튼을 누른다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184925406.png" alt="image-20221102184925406" style="zoom:67%;" />
</p>


인스턴스를 성공적으로 생성했다.



------

## Git Bash 설정 과정

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184937086.png" alt="image-20221102184937086" style="zoom:80%;" />
</p>


먼저, 인스턴스 주소를 복사한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102184946788.png" alt="image-20221102184946788" style="zoom:80%;" />
</p>


Git Bash 를 열고



```
ssh ubuntu@인스턴스주소 -i ~/Downloads/생성했던 키파일 이름
```



을 입력한다.



인스턴스 주소는 복사했던 주소를 shift + insert 로 입력하면 된다.



```
ssh ubuntu@ec2-3-37-28-55.ap-northeast-2.compute.amazonaws.com -i ~/Downloads/t3.small.pem
```



위와 같이 입력했다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102185012931.png" alt="image-20221102185012931"  />
</p>

그다음 `sudo su -` 를 입력한다.



그러면 `ubuntu@ip~~` 가 `root@ip~~` 로 변경된다.



```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
```



위 구문을 입력해서 Oh-my-bash를 설치한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102185049113.png" alt="image-20221102185049113" style="zoom: 80%;" />
</p>


Oh My Bash 가 설치되었다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102185105763.png" alt="image-20221102185105763" style="zoom:80%;" />
</p>

`mkdir git` 으로 git 폴더를 만들고



`cd git` 으로 git 폴더로 이동한 뒤



docker 설치를 위해



```
git clone https://github.com/Kyeongrok/docker_minikube_kubectl_install
```



를 입력한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102185120767.png" alt="image-20221102185120767" style="zoom:80%;" />
</p>

`ll` 로 확인해보니 docker_minikube_kubectl_install 폴더가 있고



`cd docker_minikube_kubectl_install` 로 이동한다.



그리고 `sh docker_install.sh` 로 docker 를 설치한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102185131435.png" alt="image-20221102185131435" style="zoom:80%;" />
</p>

설치 된 후



`docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password -d mysql` 를 입력해 mysql 을 설치한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102185141671.png" alt="image-20221102185141671" style="zoom:80%;" />
</p>


`docker ps` 를 입력했을때 mysql 이 있다면 성공적으로 설치된 것이다.

<br>
---
<br>

Short Cut
{: .label .label-yellow }

```
ssh ubuntu@ec2주소 -i ~/Downloads/20221012.pem

sudo su -

bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"

mkdir git

cd git

git clone https://github.com/Kyeongrok/docker_minikube_kubectl_install

cd docker_minikube_kubectl_install

sh docker_install.sh

docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password -d mysql

```



