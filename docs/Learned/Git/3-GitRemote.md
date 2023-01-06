---
layout: post
title: "· Git 원격 저장소 그리고 pull vs fetch"
nav_order: 3
parent : Git
grand_parent: 📚Learned
permalink: docs/Learned/Git/GitRemote
---

# Git 원격 저장소 그리고 pull vs fetch
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
<br>


## 원격 저장소

<br>

```
git init --bare remote
```

> --bare 옵션은 작업은 할 수 없고 저장소로서의 역할만 할 수 있는 디렉토리를 만드는 것이다.
>
> 원격 저장소에서는 보통 작업을 하지 않는 것이, 안전하기 때문에, --bare 옵션을 사용한다.

<br>

```
git remote add origin <연결할 원격저장소 주소>
```

> 위와 같은 명령어로 특정 로컬 git 작업 디렉토리를 연결할 수 있다.
>
> `origin `은 원격 저장소에 별칭을 붙인것이다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230106212512982.png" alt="image-20230106212512982" style="zoom:80%;" />
</p>

`--bare` 옵션으로 만든 원격 저장소와 연결했다.

확인해보니, `origin` 이라는 이름으로 원격 저장소의 주소가 나타나는 것을 볼 수 있다.



<br>

연결한 뒤, local 디렉토리에서 `test.txt` 라는 파일을 생성한 뒤, 커밋하고

```
git push origin main
```

을 입력하였다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230106220420659.png" alt="image-20230106220420659" style="zoom:80%;" />
</p>

원격 저장소로 커밋한 사항이 전달되었다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230106220559626.png" alt="image-20230106220559626" style="zoom: 80%;" />
</p>

원격 저장소에서 `git log` 를 입력하니, local 에서 수행한 커밋이 전달 되었음을 확인할 수 있었다.

<br>

```
git push -u origin main
```

push 할때 `-u` 옵션을 붙이면, origin 이라는 원격 저장소를 기본으로 설정해놓는다는 의미와 비슷하다.

위 구믄을 입력한 뒤로는, 커밋한 후

```
git push
```

위 명령어만 입력해도, 원격 저장소로 커밋 상황을 전달한다.

<br>





## git pull

<br>

원격 저장소와 연결을 하고나서, 만약 다른 사람이 원격 저장소에 새로운 내용을 push 했다면,

내 local 디렉토리는 아직 변경사항을 반영하지 못했을 것이다.

따라서, `pull` 이라는 명령어로 동기화를 시켜주어야 한다.

```
git pull
```

위 명령어를 입력하면, 원격 저장소의 변동 사항이 내 로컬 디렉토리에 적용된다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230106235657253.png" alt="image-20230106235657253" style="zoom:80%;" />
</p>

git pull 을 하면, 위와 같이 원격 저장소에 있는 버전(`origin/main`)과 내 로컬 버전(`HEAD -> main`)이 일치하게 된다.



<br>

## git fetch

<br>

pull 과 비슷한 fetch는 어떤 결과를 가져올까?

현재, 원격 저장소에서 새로운 파일이 추가되었고, local 은 아직 그 파일을 가져오지 않은 상황에서 pull 을 사용하면,

이전 결과처럼, 원격 저장소의 버전과 완전히 일치한 상태가 된다.

`fetch` 를 하게 되면,

```
git log --decorate --all --oneline
```

명령어로 확인해보니

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230107000416977.png" alt="image-20230107000416977" style="zoom:80%;" />
</p>

내 로컬 저장소는, 원격 저장소의 이전 버전에 존재하는 것을 볼 수 있다.

즉, 원격 저장소가 내 로컬 버전을 앞서가고 있는 상황이다.

<br>

```
git diff HEAD origin/main
```

위 명령어를 입력하여, 원격 저장소의 상태와 local 저장소의 상태 차이를 확인할 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230107000706554.png" alt="image-20230107000706554" style="zoom:80%;" />
</p>

위와 같이, 버전의 차이점을 잘 보여주는 것을 확인했다.

```
git merge origin/main
```

차이점을 확인한 뒤, merge 를 하게 되면, 비로소 pull 과 같은 상태가 되는 것이다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230107001036612.png" alt="image-20230107001036612" style="zoom:80%;" />
</p>

위와 같이, 원격 저장소의 버전과 동일함을 알 수 있다.
