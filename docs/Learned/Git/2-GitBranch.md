---
layout: post
title: "· Git branch 기본 내용"
nav_order: 2
parent : Git
grand_parent: 📚Learned
permalink: docs/Learned/Git/GitBranch
---

# Git branch 기본 내용
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
<br>


## git branch

<br>

```
git branch <브랜치이름>
```

위와 같은 명령어로 원하는 이름의 branch를 만들 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230105221134745.png" alt="image-20230105221134745" style="zoom:80%;" />
</p>

test 라는 이름의 branch를 만들었고, 아직은 main에 위치해 있는 상태이다.

<br>

```
git checkout <브랜치이름>
```

위 명령어로 원하는 브랜치로 이동할 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230105221240933.png" alt="image-20230105221240933" style="zoom:80%;" />
</p>

위와 같이 이동한 것을 확인할 수 있다.



이동 후, 로그를 살펴보면

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230105221622215.png" alt="image-20230105221622215" style="zoom:80%;" />
</p>

새로 생성한 `test` 브랜치와 `main` 브랜치가 같은 위치(버전)에 있음도 확인할 수 있다.

<br>

새롭게 생성한 `test` 브랜치에서 새로운 파일을 생성하고 커밋한 뒤, 로그를 살펴보면

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230105224354479.png" alt="image-20230105224354479" style="zoom:80%;" />
</p>

현재 작업 중인 브랜치인 `test` 와 `main` 브랜치의 위치가 달라진 것을 확인할 수 있다.

<br>
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230105225236481.png" alt="image-20230105225236481" style="zoom:80%;" />
</p>

```
git log --branches --graph
```

위 구문을 입력하면, 간략한 그래프 형태로 브랜치를 시각적으로 확인할 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230105225442111.png" alt="image-20230105225442111" style="zoom:80%;" />
</p>

```
git log --branches --graph --oneline
```

`--oneline` 을 추가해주면 더 간략하게 확인할 수 있다.





그렇다면, `test` 브랜치에서 작업한 내용을 `main` 브랜치로 어떻게 옮길 수 있을까? (⚠`main` 에서 작업한 내용을 `test` 로 옮기는 것이 아니다!)

합쳐질 브랜치로 이동한 후 합치고 싶은 브랜치를 가져와야 한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230105230930959.png" alt="image-20230105230930959" style="zoom:80%;" />
</p>

위와 같이 명령어를 입력해서 merge 를 했고, log를 살펴보면

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230105231025046.png" alt="image-20230105231025046" style="zoom:80%;" />
</p>

의도했던 대로, `main` 브랜치에 내용이 합쳐진 것을 볼 수 있다.

**참고로, test 브랜치는 merge 되었다고해서 사라진 것이 아니다.**

```
git branch -d <삭제할 브랜치명>
```

위 명령어로 삭제할 수 있다.



<br>

## git merge

<br>

새로운 브랜치를 만들고, 그 브랜치에서 작업한 파일이 main에서 작업하는 파일과 이름이 같으면서 내용 위치가 겹치면 merge하는 과정에서 충돌이 일어날 수 있다.

main에서 `common.txt` 라는 파일을 만들었고,

```
공통된 부분일꺼야
아마
```

위 문구를 작성하였고, test 브랜치로 이동하여

```
commmon text
```

라는 내용을 입력하였다.

<br>

그리고 main 으로 checkout 한 뒤, merge 를 시도하였더니 아래와 같은 상황이 일어났다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230106205848504.png" alt="image-20230106205848504" style="zoom:80%;" />
</p>

충돌되었다는 문구가 나왔고 두 브랜치에 존재했던 같은 이름의 파일인 `common.txt` 파일에서 충돌이 일어났다.

```
<<<<<<< HEAD
공통된 부분일거야
아마
=======
common text
>>>>>>> test
```

위 와 같은 내용으로 변경이 되었는데, 충돌이 일어난 부분을 표시한 것이다.

충돌시, 충돌이 일어난 파일에

어떤 브랜치에서 어떤 내용이 있었는지 알려주기 때문에, 적절하게 직접 수정을 하고 커밋을 하면 된다.

