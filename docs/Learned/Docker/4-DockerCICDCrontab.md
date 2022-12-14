---
layout: post
title: "· crontab 설정을 통한 자동 업데이트 배포 pipeline 구축"
nav_order: 4
parent : Docker
grand_parent: 📚Learned
permalink: docs/Learned/Docker/DockerCICDCrontab
---

# crontab 설정을 통한 자동 업데이트 배포 pipeline 구축
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



## 과정

<br>

1. 깃랩에 프로젝트를 올린다.


2. 배포하는 pipeline을 구축한뒤, 배포에 필요한 환경변수를 전달하고 run한다.


3. crontab을 활용해서 깃랩에 변경사항이 생기면, 변경사항을 감지하고 새로운 프로젝트로 자동 배포가 되도록 한다.

<br>

## 1. Dockerfile 루트 디렉토리에 추가하기

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215020555773.png" alt="image-20221215020555773" style="zoom:80%;" />
</p>

배포하고자 하는 프로젝트의, 제일 왼쪽 Gradle 버튼을 누른 뒤,  톱니바퀴 모양의 `build`를 더블클릭한다.
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215020726625.png" alt="image-20221215020726625"  />
</p>

그러면, `build > libs` 디렉토리가 생기고, `.jar` 파일이 생성된다.

<br>

```dockerfile
FROM gradle:7.4-jdk11-alpine as builder
WORKDIR /build

# 그래들 파일이 변경되었을 때만 새롭게 의존패키지 다운로드 받게함.
COPY build.gradle settings.gradle /build/
RUN gradle build -x test --parallel --continue > /dev/null 2>&1 || true

# 빌더 이미지에서 애플리케이션 빌드
COPY . /build
RUN gradle build -x test --parallel

# APP
FROM openjdk:11.0-slim
WORKDIR /app

# 빌더 이미지에서 jar 파일만 복사 (생성한 jar 파일과 이름이 같아야함)
COPY --from=builder /build/build/libs/web-0.0.1-SNAPSHOT.jar .

EXPOSE 8080

# root 대신 nobody 권한으로 실행
USER nobody
ENTRYPOINT [                                                \
   "java",                                                 \
   "-jar",                                                 \
   "-Djava.security.egd=file:/dev/./urandom",              \
   "-Dsun.net.inetaddr.ttl=0",                             \
   "web-0.0.1-SNAPSHOT.jar"              \
   # 생성한 jar 파일과 이름이 같아야함
]
```

> 위와 같은 내용이 담긴 Dockerfile을 추가한다.
>
> 생성한 `jar` 파일의 이름에 맞게 `COPY` 구문과 `ENTRYPOINT` 구문을 적절히 수정한다.

<br>

## 2. 프로젝트 깃랩에 올리기

<br>

`jar` 파일을 생성하고, `Dockerfile` 을 추가하였다.

[병·의원 웹페이지 프로젝트](https://github.com/inkyu-yoon/hospital_web)

위 프로젝트를 먼저 GitLab에 업로드 해보겠다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215020117257.png" alt="image-20221215020117257" style="zoom:67%;" />
</p>

이미 깃허브 Repository가 존재하므로, Import project를 선택하면 간단하게 옮길 수 있다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215020309027.png" alt="image-20221215020309027" style="zoom:67%;" />
</p>

`hospital_web` 레포지토리를 `import` 하면, GitLab 프로젝트가 생성된다.

<br>

## 3. 환경변수 등록하기

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215021323077.png" alt="image-20221215021323077" style="zoom: 67%;" />
</p>

생성된 프로젝트에 들어가서 `CI/CD > Editor` 에 들어간다.

<br>
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215021359208.png" alt="image-20221215021359208" style="zoom: 50%;" />
</p>

`Configure pipeline` 을 클릭한다.

```yaml
stages:
  - dockerbuild-push

package:
  image: docker:latest
  stage: dockerbuild-push
  services:
    - docker:dind
  before_script:
    - docker login registry.gitlab.com -u $GITLAB_USER -p $GITLAB_PASSWORD
  script:
    - docker build -t registry.gitlab.com/$GITLAB_USER/$PROJECT_NAME .
    - docker push registry.gitlab.com/$GITLAB_USER/$PROJECT_NAME
  after_script:
    - docker logout
```

해당 내용을 채운뒤, `Commit changes` 버튼을 누른다.

`$GITLAB_USER`, `$GITLAB_PASSWORD`, `$PROJECT_NAME` 은 환경변수로 입력해주어야 한다.

<br>

먼저,

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215025705480.png" alt="image-20221215025705480" style="zoom:80%;" />
</p>

`Settings > CI/CD > Variables` 에 환경변수를 등록해준다.

<br>


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215021841498.png" alt="image-20221215021841498" style="zoom: 80%;" />
</p>

그리고나서 `CI/CD > Pipelines` 에 들어가면, `failed` 가 될 것이다. 아직은, 환경변수를 전달해주지 않았기 때문에, 당연한 결과다.

오른쪽 상단 `Run pipeline` 버튼을 누른다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215022034260.png" alt="image-20221215022034260" style="zoom:80%;" />
</p>

환경변수에 적절한 값을 입력하고 `Run pipeline`을 입력한다.



<br>


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215022454862.png" alt="image-20221215022454862" style="zoom:80%;" />
</p>

환경변수를 전달했더니, passed 되었다!

> 이 과정에서, 오류가 발생했다면, user 환경변수에 대문자가 입력되어 있거나 password 환경변수에 `@`를 제외한 특수문자가 들어갔을 경우 발생할 수 있다.



<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215022716528.png" alt="image-20221215022716528" style="zoom:80%;" />
</p>

`Packages and registries > Container Registry` 에 들어가면, `hospital_web` 컨테이너가 생성된 것을 확인할 수 있다.

컨테이너 명 옆에 이모티콘을 클릭하여 경로를 복사한다.

```
registry.gitlab.com/inkyu-yoon/hospital_web
```

위와 같은 경로를 얻을 수 있다.

<br>

## 4. 컨테이너 실행 후, deploy.sh 와 crontab 설정

<br>

[ec2 서버 생성 + docker 설치 + mysql 연결](https://inkyu-yoon.github.io/docs/Learned/Docker/DockerAndEc2)

위 과정 대로 진행하여, ec2 서버를 열고 docker와 mysql을 설치한다.



<br>

```
docker pull registry.gitlab.com/inkyu-yoon/hospital_web
```

위 구문을 입력하여 컨테이너를 가져온 뒤, 실행시킨다.

<br>

```
docker run -p 8080:8080 --name hospital_web -e SPRING_DATASOURCE_URL=jdbc:mysql://ec2-43-206-113-188.ap-northeast-1.compute.amazonaws.com:3306/inkyu-db -e SPRING_DATASOURCE_PASSWORD=password -e JWT_TOKEN_SECRET=hello -d registry.gitlab.com/inkyu-yoon/hospital_web
```

위 구문을 입력하여, `hospital_web` 이라는 컨테이너 이름으로 어플리케이션을 구동하는데 필요한 환경변수들을 전달하고

마지막에 `-d registry.gitlab.com/inkyu-yoon/hospital_web` 이 구문을 꼭 붙여준 뒤, 컨테이너를 실행시킨다.

<br>
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221215023607284.png" alt="image-20221215023607284" style="zoom:80%;" />
</p>

위와 같이 2개의 컨테이너가 실행중인 것을 확인할 수 있다.

`ec2주소:8080` 에 접속하면, 정상적으로 어플리케이션이 배포되는 것을 확인할 수 있다.

<br>


```
set -ex

docker pull registry.gitlab.com/inkyu-yoon/hospital_web:latest | grep "Image is up to date" && pull_status="already_pulled" || pull_status="newly_pulled"

echo $pull_status

if [ "$pull_status" = "newly_pulled" ]; then
        docker stop hospital_web
        docker rm -f hospital_web
        docker run -p 8080:8080 --name hospital_web -e SPRING_DATASOURCE_URL=jdbc:mysql://ec2-43-206-113-188.ap-northeast-1.compute.amazonaws.com:3306/inkyu-db -e SPRING_DATASOURCE_PASSWORD=password -e JWT_TOKEN_SECRET=hello -d registry.gitlab.com/inkyu-yoon/hospital_web
fi

```

위 구문이 담긴 `deploy.sh` 파일을 `/root/home` 디렉토리를 만든 뒤, 해당 위치에 저장해둔다.

해당 프로젝트에 변동 사항이 있다면, 자동으로 원래 있던 컨테이너를 삭제한 뒤, 새로운 컨테이너를 실행시키도록 하는 구문이다.

<br>

```
crontab -e
```

위 구문을 입력해서 crontab 설정을 입력한다.

<br>

```
* * * * * sh /root/home/deploy.sh >> /root/home/deploy.log
```

crontab 안에, 위 구문을 작성하면, 매분마다, deploy.sh 파일을 확인해서, deploy.sh 파일을 실행하게 된다면 log를 남기도록 설정해두었다.

<br>

이제, 해당 GitLab 프로젝트 main 브랜치에 새로운 커밋사항이 들어오면, 자동으로 빌드 된 뒤 배포가 되는 것을 확인할 수 있다.

