---
layout: post
title: "· javascript async vs defer 옵션"
nav_order: 4
parent : FrontEnd
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/FrontEnd/JsDefer
---

# javascript async vs defer 옵션
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

## 상황 별, 자바스크립트 fetching 과 executing 시점

<br>

기본적으로 html 구문을 위에서 부터 한줄 한줄씩, parsing 하면서 읽어나간다.

<br>



### head 태그 안에 script 태그가 있는 경우

<br>



```html
<!DOCTYPE html>
<html lang = "en">
	<head>
	<meta charset="UTF-8" />
	<title>Document</title>
	<script src = "main.js"></script>
</head>
<body></body>
</html>
```

<br>

위와 같이 구성되어 있다고 가정하면, 위에서 부터 내려오면서 HTML 구문을 parsing 하다가 `<script>` 태그를 발견하면

해당 스크립트 태그의 src 파일을 서버에서 다운로드(fetching) 하고 실행(executing) 하게 된다.

그러고 나서 남은, HTML 구문을 parsing 한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230114001347411.png" alt="image-20230114001347411" style="zoom: 50%;" />
</p>

그림으로 보면 위와 같은 과정을 거친다.

<br>

자바스크립트 파일이 커서 다운로드하고 실행하는데 오래 걸린다면, 완전한 HTML 파일을 보기까지 시간이 오래 걸린다는 단점이 있다.



<br>



### body 태그 끝 부분에 script 태그가 위치하는 경우

<br>

```html
<!DOCTYPE html>
<html lang = "en">
	<head>
	<meta charset="UTF-8" />
	<title>Document</title>
</head>
<body>
    <script src = "main.js"></script>
</body>
</html>
```



위와 같이 코드가 구성되어 있다고 가정하면, HTML 파일을 모두 parsing 한 뒤, scipt 태그를 발견하여 다운로드받고 실행할 것이다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230114001749439.png" alt="image-20230114001749439" style="zoom: 50%;" />
</p>

<br>

사용자가 기본적인 HTML 컨텐츠를 빠르게 볼 수 있는 장점이 있지만, 자바스크립트에 의존적인 사이트의 경우는 정상적인 페이지를 보기까지 오래 걸릴 수 있다.



<br>

### head 태그안에 script 태그에 async 옵션을 사용하는 경우

<br>

```html
<!DOCTYPE html>
<html lang = "en">
	<head>
	<meta charset="UTF-8" />
            <script asyn src = "main.js"></script>
	<title>Document</title>
</head>
<body>
</body>
</html>
```

위와 같이 코드가 구성되어 있다고 할때,

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230114002046784.png" alt="image-20230114002046784" style="zoom: 50%;" />
</p>

script의 async 옵션을 확인하면, 병렬로 다운로드 받도록 요청한다.

자바스크립트 파일 저장이 완료되면, HTML 을 parsing 하는 것을 멈추고 다운받은 자바스크립트 파일을 실행시킨다.

실행이 끝난 뒤, 남은 HTML 을 parsing 한다.

<br>

다운로드 받는 시간을 절얄할 수 있지만, HTML이 완전이 parsing 이 끝나기 전에 실행되므로 완전한 페이지를 확인하는 시간을 단축하기는 어려울 수 있다.

<br>

### head 태그안에 script 태그에 defer 옵션을 사용하는 경우

<br>

```html
<!DOCTYPE html>
<html lang = "en">
	<head>
	<meta charset="UTF-8" />
            <script defer src = "main.js"></script>
	<title>Document</title>
</head>
<body>
</body>
</html>
```



위와 같이 코드가 구성되어 있다면,

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230114002407727.png" alt="image-20230114002407727" style="zoom: 50%;" />
</p>

script 의 defer 옵션을 확인하면, 병렬적으로 다운로드 받도록 하고, 실행은 HTML parsing 이 끝나면 진행하도록 한다.

또한, defer 옵션이 적용된 script가 여러개라면, 다운된 순서와 관계 없이, 발견한 순서대로 실행시킬 수 있어 안전한 웹 사이트 표현이 가능해진다.