---
layout: post
title: "· Dockerfile 최적화 작성 및 빌드"
nav_order: 6
parent : Docker
grand_parent: 📚Learned
permalink: docs/Learned/Docker/DockerDockerfile
---

# Dockerfile 최적화 작성 및 빌드

<br>



## 도커파일 예시

<br>

```
FROM diamol/node

ENV TARGET="bolg.sixeyed.com"
ENV METHOD="HEAD"
ENV INTERVAL="3000"

WORKDIR /web-ping
COPY app.js . 

CMD ["node","/web-ping/app.js"]
```

<br>

### FROM

<br>

- 모든 이미지는 다른 이미지로부터 출발한다. 위 예시에서는 `diamol/node` 이미지를 시작점으로 사용했는데,  애플리케이션을 실행하는데 필요한 런타임인 Node.js 가 설치되어 있다.

<br>

### ENV
<br>


- 환경 변수 값을 지정하기 위한 인스트럭션

<br>

### WORKDIR
<br>


- 컨테이너 이미지 파일 시스템에 디렉터리를 만들고 해당 디렉터리를 작업 디렉터리로 지정한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221218164430167.png" alt="image-20221218164430167" style="zoom:120%;" />
</p>

> 해당 스크립트로 만들어진 컨테이너에 `pwd` 명령어를 전달하면 위와 같이 `WORKDIR` 로 지정한 디렉터리가 나온다.

<br>

### COPY

<br>

- 로컬 파일 시스템의 파일 혹은 디렉터리를 컨테이너 이미지로 복사한다. 위 예시의 경우 `app.js` 파일을 이미지의 작업 디렉터리로 복사했다.

<br>

### CMD

<br>

- 도커가 이미지로부터 컨테이너를 실행했을 때, 실행할 명령을 지정하는 인스트럭션이다. 위의 예시의 경우, `Node.js` 런타임이 `app.js` 를 시작하도록 했다.

<br>

## Dockerfile 스크립트 최적화

<br>

위의 예시 스크립트에서 사용하는 `app.js` 자바스크립트 파일이 변경이 일어나면, 캐시된 것을 사용하면 안되기 때문에 새로운 레이어를 생성한다.

변경이 일어난 단계 이후는, 변경 유무와 관계 없이 재 실행된다.

<br>

{: .important }
> 따라서, Dockerfile 스크립트의 인스트럭션은 잘 수정하지 않는 인스트럭션이 앞으로 오고 자주 수정되는 인스턱션이 뒤에 오도록 배치해야 한다. 
> 
> 이렇게 해야 캐시에 저장된 이미지 레이어를 되도록 많이 재사용할 수 있기 때문이다.



<br>

따라서 위 예시를 최적화하면 아래와 같이 할 수 있다.

```
FROM diamol/node

CMD ["node","/web-ping/app.js"]

ENV TARGET="bolg.sixeyed.com" \
    METHOD="HEAD" \
    INTERVAL="3000"

WORKDIR /web-ping
COPY app.js . 
```

<br>

## build 와 commit 명령어

<br>

> Dockerfile로 작성한 스크립트로 이미지를 생성하고 싶은 경우, **build** 명령어를 사용한다.
> 
> 만약, 컨테이너를 실행한 뒤, 도커 명령어나, 리눅스 명령어로 컨테이너를 수정한 뒤에, 새로운 이미지로 생성하고 싶은 경우
> **commit** 명령어를 사용해서, 새로운 이미지를 생성할 수 있고, run으로 컨테이너에 접속하거나 실행하면 된다.