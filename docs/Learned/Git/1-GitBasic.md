---
layout: post
title: "· Git 기본 명령어 정리"
nav_order: 1
parent : Git
grand_parent: 📚Learned
permalink: docs/Learned/Git/GitBasic
---

# Git 기본 명령어 정리
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
<br>

## 사용자 정보 등록

```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

위와 같은 명령어로 사용자 등록을 진행하고, 만약 프로젝트마다 다른 이름과 이메일 주소를 사용하고 싶다면, `--global` 옵션을 빼고 명령을 실행하면 된다.




<br>

## git init

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221231232120173.png" alt="image-20221231232120173" style="zoom: 67%;" />
</p>

```
git init
```

현재 위치해있는 디렉토리 내의 파일들의 버전 관리를 시작하는 명령어이다.

해당 디렉토리에 `.git` 이라는 디렉토리가 하나 생기게 된다.

(기존에 존재하는 Git 저장소를 clone 해서 시작하는 방법도 있다.)

<br>

## git add

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221231232647312.png" alt="image-20221231232647312" style="zoom:67%;" />
</p>

text1.txt 라는 파일을 생성했다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221231232834190.png" alt="image-20221231232834190" style="zoom:67%;" />
</p>

```
git status
```

라는 명령어로 확인해보니, text1.txt 파일이 untracked 되어 있었다.

<br>

따라서,

```
git add 파일명
```

으로 추가해주었고, 다시 확인해보니, 해당 파일을 깃이 관리하기 시작했음을 알 수 있다.

`stage area` 라는 곳에 올라가게 되어, commit 되기를 기다리고 있는 중이라고도 볼 수 있다.

<br>

## git commit

<br>

```
git commit
```

위 구문을 입력하면,


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221231233407931.png" alt="image-20221231233407931" style="zoom:80%;" />
</p>

위와 같은 vim 화면이 나타나고, 이 화면에 커밋 메세지를 입력하고 `:wq` 로 저장하고 나오면

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221231233426326.png" alt="image-20221231233426326" style="zoom:80%;" />
</p>

버전 관리를 요청한 파일의 변경 사항을 저장할 수 있게 된다.

```
git log
```

위 명령어를 입력하면

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221231233640873.png" alt="image-20221231233640873" style="zoom:67%;" />
</p>

커밋 메세지를 확인할 수 있다.

<br>

```
git commit -m "원하는 메세지"
```

위와 같은 방식으로 바로 커밋을 할 수도 있다.

<br>

```
git commit -am "원하는 메세지"
```

**위와 같은 방법을 사용하면, git 관리가 시작된 파일(tracked 된)이 변경사항이 일어난 경우 add를 따로 할 필요없이 바로 커밋할 수 있다.**

새로 생성되어, 아예 untracked 된 파일은 따로 add 를 해주어야 한다.

<br>

```
git commit --amend
```

위 명령어를 사용하면, 만약 빠뜨린 파일을 커밋하지 못했을 때, 사용할 수 있는 명령어이다.

뭔가 빠뜨린 것을 넣거나 변경하는 것을 새 커밋으로 분리하지 않고 하나의 커밋에서 처리한다.

<br>

## git log

<br>

```
git log -p
```

위 명령어를 입력하면, 각 커밋시점 마다의 차이점을 확인할 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230101113657035.png" alt="image-20230101113657035" style="zoom:80%;" />
</p>

위와 같이, 특정 파일에 내용이 추가된 것도 보이고 새로운 파일이 추가된 것도 보인다.

<br>

## git reset

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230101114602411.png" alt="image-20230101114602411" style="zoom:80%;" />
</p>

현재 위와 같은 상황이라고 했을 때, 두번째 버전으로 돌아가고 싶다면 어떻게 해야할까?


두번째 버전 커밋 id를 복사한 뒤,

```
git reset <커밋id> --hard
```

를 입력하면, 해당 커밋 id가 제일 최신인 상태로 돌아갈 수 있다.

