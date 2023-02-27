---
layout: post
title: "· Docker Compose와 Git Actions로 CI/CD 구현하기"
nav_order: 7
parent : Docker
grand_parent: 📚Learned
permalink: docs/Learned/Docker/GitActionsCICD
---

# Docker Compose와 Git Actions로 CI/CD 구현하기

<br>

## 도커 허브 레포지토리 생성

<br>

[https://hub.docker.com/](https://hub.docker.com/)

먼저 위 도커 허브 사이트로 가서, 회원가입을 하고 repository를 만든다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220215545319.png" alt="image-20230220215545319" style="zoom:67%;" />
</p>

위와 같은 레포지토리를 하나 만들었다.

<br>

## 도커 컴포즈 다운

<br>

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.28.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

위 구문을 입력해서 도커 컴포즈를 다운받는다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220214642598.png" alt="image-20230220214642598"  />
</p>

<br>

```
chmod +x /usr/local/bin/docker-compose
```

위 구문을 입력해서 compose 실행 권한을 얻자.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220214731477.png" alt="image-20230220214731477"  />
</p>

<br>

구문을 입력하지 않으면 위 사진 처럼, `Permission denied` 가 뜬다.

<br>

이제 docker compose를 위한 yml 파일을 작성한다.

<br>

`compose` 라는 디렉토리를 만들고, 그 안에 `vim docker-compose.yml` 명령어로 아래와 같은 구문을 작성하였다.

```
version: '3'
services:
  web:
    container_name: sns
    image: tryingmybest24h/sns
    ports:
      - '8080:8080'
```

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220215723577.png" alt="image-20230220215723577" style="zoom:80%;" />
</p>

image는 처음에 도커허브에 만들었던 레포지토리 이름과 동일하다.

<br>

## DockerFile 작성

<br>

```dockerfile
FROM openjdk:11
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
ENV JAVA_OPTS="-Dcom.amazonaws.sdk.disableEc2Metadata=true"
ENTRYPOINT ["java", "-Dspring.profiles.active=sns", "-jar", "/app.jar"]
```

위와 같은 `Dockerfile` 을 작성한다. (이름 꼭 `Dockerfile`로 해야함 `DockerFile` 안됨!!)

`-Dspring.profiles.active=sns` 는 `application-sns.yml` 로 관리하기 때문이다.

<br>

## Git Actions CI/CD 스크립트

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220220326538.png" alt="image-20230220220326538" style="zoom: 150%;" />
</p>

위와 같은 경로에 `cicd.yml` 파일을 만든다.

```
# github repository Actions 페이지에 나타낼 이름
name: CI/CD

# event trigger
on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          token: ${{ secrets.CHECKOUT_TOKEN }}
          submodules: true
      - name: Validate Gradle Wrapper
        uses: gradle/wrapper-validation-action@v1
      - name: Set up JDK11
        uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '11'
          cache: 'gradle'
      - name: Grant execute permission for gradlew
        run: chmod +x gradlew
      - name: Execute Gradle build
        run: ./gradlew clean build -x test -Pprofile=sns #application 프로필 이름
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      - name: Docker Login
        uses: docker/login-action@v2
        with:
          username: ${{secrets.DOCKER_USER}}
          password: ${{secrets.DOCKER_PASSWORD}}
      - name: build and release to DockerHub
        env:
          NAME: ${{secrets.DOCKER_USER}}
          APP: sns
        run: |
          docker build -t $NAME/$APP -f ./Dockerfile . # 여기서 DockerFile 로 썼다가 에러남..
          docker push $NAME/$APP:latest
      - name: EC2 Docker Run
        uses: appleboy/ssh-action@master
        env:
          APP: "sns"
          COMPOSE: "/root/compose/docker-compose.yml"
        with:
          username: ubuntu
          host: ${{secrets.EC2_HOST}}
          key: ${{secrets.EC2_KEY}}
          envs: APP, COMPOSE
          script_stop: true
          script: |
            sudo docker-compose -f $COMPOSE down --rmi all
            sudo docker pull ${{secrets.DOCKER_USER}}/$APP:latest
            sudo docker-compose -f $COMPOSE up -d
```

<br>
main 에 push 했을때만 동작하도록 설정하였다.

<br>

또한, 스프링부트 2.5버전 이후로는 jar 파일이 2개 생겨 Git Actions 작업이 제대로 작동하지 않을 수 있다. 따라서 build.gradle에

```
jar {
  enabled = false
}
```

위 구문을 추가한다.

<br>

모든 설정을 마쳤다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220223751422.png" alt="image-20230220223751422" style="zoom:80%;" />
</p>

<br>

push  했더니, 컨테이너가 잘 올라갔다.!!

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220224314011.png" alt="image-20230220224314011" style="zoom:67%;" />
</p>

Git Actions도 에러 없이 잘 돌아갔다.

<br>

{: .highlight }
참고 블로그 : [https://percyfrank.github.io/infra/Infra01/](https://percyfrank.github.io/infra/Infra01/)
