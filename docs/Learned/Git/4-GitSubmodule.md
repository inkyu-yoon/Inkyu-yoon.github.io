---
layout: post
title: "· Git Submodule을 이용해 환경변수 관리하기"
nav_order: 4
parent : Git
grand_parent: 📚Learned
permalink: docs/Learned/Git/GitSubmodule
---

# Git Submodule을 이용해서 안전하고 유지보수하기 쉽게 환경변수 관리하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
<br>

Git의 서브모듈은 Git 저장소 안에 다른 Git 저장소를 디렉토리로 분리해 넣는 것이다. 

다른 독립된 Git 저장소를 Clone 해서 내 Git 저장소 안에 포함할 수 있으며 각 저장소의 커밋은 독립적으로 관리한다.

Git 서브모듈을 사용해서 private repository에 환경변수를 저장하여 

docker 컨테이너 실행 시, 복잡하게 환경변수를 입력해주지 않아도 되서 유지보수성을 향상시킬 수 있고

private repository에 저장하여 권한 없이 접근할 수 없도록 하여 보안을 향상시켜보도록 해보자.

<br>

## private repository 생성

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220212133711.png" alt="image-20230220212133711" style="zoom:80%;" />
</p>

환경변수가 들어있는 yml 파일을 private repository에 생성한다.

<br>

## 프로젝트와 private repository 연결

<br>

```
git submodule add <private repository 주소>
```

위 구문을, terminal에 입력한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220212417121.png" alt="image-20230220212417121" style="zoom:67%;" />
</p>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220212511417.png" alt="image-20230220212511417" style="zoom: 50%;" />
</p>

입력하니 `sns-env` 디렉토리와 `.gitmodules` 라는 파일이 생성되었다.



```groovy
task copyPrivate(type: Copy) {
    copy {
        from './sns-env' //위 사진과 같은 폴더 명
        include "application-sns.yml" //위 폴더안에 있는 yml파일 명
        into 'src/main/resources' //복사할 위치
    }
}
```

build.gradle에 위 구문을 추가한다. (private repository로부터 주입받은 파일을 실제로 존재하게 만들어준다.)

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220212843787.png" alt="image-20230220212843787" style="zoom:80%;" />
</p>

다시 build하면 위와 같이 `application-sns.yml` 파일이 resource 폴더에 생성된다.

이 yml 파일은 커밋되어 원격 저장소로 가면 안된다. 주의하자.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220213006246.png" alt="image-20230220213006246" style="zoom:80%;" />
</p>

로컬에서 실행할땐, 위와같이 `Active profiles` 에 sns를 적어주면 된다. (가져온 파일이 `application-sns.yml` 이므로 )

<br>

커밋하고, 푸시한 뒤 깃허브 프로젝트 레포지토리에 가보면

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230220213640717.png" alt="image-20230220213640717" style="zoom: 80%;" />
</p>

잘 적용된것을 확인할 수 있다.

{: .highlight }
참고 블로그 : [https://percyfrank.github.io/infra/Infra01/](https://percyfrank.github.io/infra/Infra01/)

