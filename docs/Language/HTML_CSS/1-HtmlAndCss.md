---
layout: default
title: "· HTML / CSS 기초"
nav_order: 1
parent : FrontEnd
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/HTML_CSS/HtmlAndCss
---

# HTML / CSS 기초
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



## html 기본 형식

<br>

html의 문서는

{% capture some_var %}
```html
<!DOCTYPE html>
<html>

</html>
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


위와 같은 제일 큰 틀을 먼저 생성한 후 안에 내용들을 넣는다.



그리고 한국어를 인식할 수 있게, `<meta charset = "UTF-8">` 코드를 태그에 추가해주어야 한다.



---



## html의 간단한 구조


<br>

{% capture some_var %}
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset = "UTF-8">
		<title> 타이틀 제목입니다.</title>
	</head>
	<body>
		<h1> 게시글 큰 제목입니다.</h1>
		<p> 내용입니다. </p>
        <footer>반갑습니다..</footer>
	</body>
</html>
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


위와 같이 코드를 입력하면

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017160724358.png" alt="image-20221017160724358" style="zoom: 67%;" />
</p>



이러한 결과를 얻을 수 있다.

<br>

`<body></body>` 태그 안에 있는 내용들은 html 문서에 직접적으로 표현되는 내용을 넣는 태그이다.



`<footer></footer>` 태그는, html 문서 제일 아래에 원하는 내용을 표시하는 태그이다.



---



## CSS 적용

<br>

html 은 간단하게 말하면, 골격과 구조를 결정하는 코드이고, 그 외에 내용의 색이나, 크기 등 스타일을 설정하기 위해서는 css 코드를 작성해야 한다.



css를 적용하기 위해서는 css 코드가 작성된 css 파일을 연결시켜주어야 하는데, head 태그 안에 `<link rel="stlyesheet" href="css파일이름.css">` 을 넣어주면된다.



예를 들어, `codelion.css` 파일이 있다고 할 때,
{% capture some_var %}
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset = "UTF-8">
		<title> 타이틀 제목입니다.</title>
        <link rel = "stylesheet" href="codelion.css">
	</head>
	<body>
		<h1> 게시글 큰 제목입니다.</h1>
		<p> 내용입니다. </p>
        <footer>반갑습니다..</footer>
	</body>
</html>
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


html 파일을 위와 같이 작성하고


{% capture some_var %}
```css
footer{
    text-align : center;
    background-color:black;
    color:white;
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

위와 같이 css 파일을 적용한다면,


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017161514131.png" alt="image-20221017161514131" style="zoom:67%;" />
</p>

위와 같이, footer 부분의 스타일이 검은색 배경에 하얀색 글자, 그리고 가운데 정렬이 된 것을 확인할 수 있다.

즉, 특정 태그의 스타일을 결정하는 것이 css 파일이라고 볼 수 있다.

이 외에도, 아래와 같은 설정들이 있다.

<br>

`font-size : ()px;` : 폰트 사이즈 조절

`color : ();` : 폰트 색 설정 (black,red 등등)

`font-weight : ();`  : 폰트 두께 설정 (bold, or 숫자 등등)

`line-height : ()px; ` : 폰트 간격 설정( 💡 일반적으로 폰트 크기의 160%로 설정한다)

---



## style를 구분해서 적용하기

<br>

위와 같은 방법으로는, 스타일이 적용된 모든 태그가 스타일이 결정되므로, 원하는 부분만 스타일을 적용할 수 없다.



이때, class를 적용시켜, 적용하길 원하는 스타일을 나눠서 적용할 수 있다.


{% capture some_var %}
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset = "UTF-8">
		<title> 타이틀 제목입니다.</title>
        <link rel = "stylesheet" href="codelion.css">
	</head>
	<body>
		<h1> 게시글 큰 제목입니다.</h1>
		<p class = "ex1"> 첫번째 내용입니다. </p>
        <p class = "ex2"> 두번째 내용입니다. </p>
        <p class = "ex3"> 세번째 내용입니다. </p>
        <footer>반갑습니다..</footer>
	</body>
</html>
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


위와 같이 태그에 class를 사용해서 이름을 붙여주고


{% capture some_var %}
```css
footer{
    text-align : center; 
    background-color:black; 
    color:white;
}

.ex1{
    font-size : 30px;
}

.ex2{
    font-size : 40px;
}

.ex3{
    font-size : 10px;
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


위와 같이, 정해준 이름 앞에 `.` 을 붙여주고 스타일을 적용시켜주면


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017162536519.png" alt="image-20221017162536519" style="zoom:67%;" />
</p>

위와 같이 각 <p> 태그마다 스타일이 적용된 것을 볼 수 있다.



https://htmlcolorcodes.com/

💡 TIP : 위 사이트에서 color 코드를 얻고, 다양한 색을 표현할 수 있다.



---



## div 태그 적용

<br>

여러 태그의 스타일을 한번에 적용하기 위해서는 `<div></div>` 태그로 감싸주고 스타일을 적용시키면 된다.



`border : 두께 방식 색깔 ;` 로 박스를 만들 수 있다.

`width : 너비;` 로 박스의 너비를 설정할 수 있다.

`margin-left : auto; margin-right : auto;` 를 사용하면 박스를 가운대 정렬시킬 수 있다.


{% capture some_var %}
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset = "UTF-8">
		<title> 타이틀 제목입니다.</title>
        <link rel = "stylesheet" href="codelion.css">
	</head>
	<body>
		<h1> 게시글 큰 제목입니다.</h1>
        <div class = "div1">
        	<p class = "ex1"> 첫번째 내용입니다. </p>   
        </div>
        
        <div class = "div2">
		    <p class = "ex2"> 두번째 내용입니다. </p>
            <p class = "ex3"> 세번째 내용입니다. </p>
            <footer>반갑습니다..</footer>
        </div>
	</body>
</html>
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


위와 같이 2개의 div 박스를 만들었다.


{% capture some_var %}
```css
footer{
    text-align : center; 
    background-color:black; 
    color:white;
}

.ex1{
    font-size : 30px;
}

.ex2{
    font-size : 40px;
}

.ex3{
    font-size : 10px;
}
.div1{
    border : 3px solid blue;
    width : 610px;
    margin-left : auto;
    margin-right : auto;
}
.div2{
    border : 5px solid red;
    width : 500px;
    margin-left : auto;
    margin-right : auto;
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


그리고 위와 같은 css 파일을 적용시켰을 때


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017164442667.png" alt="image-20221017164442667" style="zoom:67%;" />
</p>

위와 같은 결과를 얻을 수 있다.



`div` 뿐만 아니라 `section` `article` 등으로 콘텐츠를 분리할 수 도 있다. 

다양한 태그를 적절한 곳에 활용했을때, 가독성을 높일 수 있다.



---



## 박스 꾸미기

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017170033588.png" alt="image-20221017170033588" style="zoom: 67%;" />
</p>



박스는 margin + border + padding + contents(width & height) 박스로 이루어져있고, 각 사이즈를 조절해서 원하는 형태의 박스를 만들 수 있다.

{% capture some_var %}
```css
footer{
    text-align : center; 
    background-color:black; 
    color:white;
}

.ex1{
    font-size : 5px;
}

.ex2{
    font-size : 5px;
}

.ex3{
    font-size : 5px;
}
.div1{
    height : 100px;
    width : 100px;
    margin : 20px;
    border : 20px solid black;
    padding: 20px;
}
.div2{
    height : 100px;
    width : 100px;
    border : 20px solid red;
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


css파일을 위와 같이 적용해보면


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017171220159.png" alt="image-20221017171220159" style="zoom: 50%;" />
</p>



위와 같은 결과를 얻을 수 있고, 두 박스의 contents 박스의 크기를 결정하는 height 와 width와 border 사이즈는 동일하다.



하지만 첫번째 박스의 경우 margin 사이즈와 padding 사이즈도 존재하여 위와 같은 결과가 나온다.



`(margin/border/padding)-(top/bottom/right/left)` 와 같이 원하는 방향만 공간을 생성할 수도 있다.



---

박스에 그림자를 설정하여 넣을 수도 있다.



`box-shadow : (그림자 가로방향 상대위치) (그림자 세로방향 상대위치) (그림자 흐리게) (그림자 크기) (그림자 색)`

<br>

첫번째 인자는, 양수값은 그림자가 오른쪽으로, 음수 값은 그림자가 왼쪽으로 생긴다

두번째 인자는, 양수값은 그림자가 아래쪽으로, 음수 값은 그림자가 위로 생긴다.

세번째 인자는, 값이 클수록 그림자를 흐리게 만든다.

네번째 인자는, 박스의 크기보다 그림자의 크기를 키우고 싶을때 넣는 값이다. 10px를 입력하면, 가로세로 크기가 박스보다 10px 큰 그림자가 생성된다.

다섯번째 인자는, 그림자의 색을 결정한다. 

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017173756804.png" alt="image-20221017173756804" style="zoom:67%;" />
</p>

<br>

`box-shadow: 20px 20px 20px 10px red;` 를 적용한 박스의 예시이다.





---



## html 폰트 적용하기

<br>

기본 폰트는 너무 딱딱하기 때문에, 폰트를 적용해본다.



https://fonts.google.com/

위 사이트에서 원하는 폰트를 찾은다음에


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017174222064.png" alt="image-20221017174222064" style="zoom: 67%;" />
</p>


원하는 폰트 스타일을 찾은 뒤, `+` 를 클릭하고, 열리는 창에서 

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017174519042.png" alt="image-20221017174519042" style="zoom:80%;" />
</p>

이 부분을 복사해서 css 파일에 입력하고

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017174621132.png" alt="image-20221017174621132" style="zoom:80%;" />
</p>

위 사진 처럼, 원하는 박스에 `font-family: 'Montserrat';` 를 입력하면 된다. (이름은 폰트마다 다르다.)

`sans-serif` 는 글자 스타일인데, `cursive`,`fantasy` 등이 있다.



---

## float

<br>

한 라인에서 특정 부분을 왼쪽, 특정 부분을 오른쪽으로 정렬시키기 위해서는 `float : (right/left); ` 를 이용해서 정렬해야 한다.

또한, float를 이용해서 정렬한 내용들이 겹쳐지지 않게 하기 위해서는 

겹쳐지는 태그를 div로 감싼 후,`overflow: hidden` 속성을 추가해주어야 한다.


{% capture some_var %}
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <link rel="stylesheet" href="codelion.css">
</head>
<body>
	</p> 안녕하세요.   작성자 </p>
</body>
</html>

```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


위와 같은 코드는


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017192148774.png" alt="image-20221017192148774" style="zoom:80%;" />
</p>

위와 같이 붙어서 출력될 것이다.

{% capture some_var %}
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="codelion.css">
  </head>
  <body>
        <p class="text1">안녕하세요</p>
        <p class="text2">작성자</p>
  </body>
</html>
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

{% capture some_var %}
```css
.text1 {
    float: left;
}

.text2 {
    float: right;
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


하지만, 각각 <p> 태그로 감싼 후, float 속성을 css 에서 위와 같이 부여하면,


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017192328132.png" alt="image-20221017192328132" style="zoom:80%;" />
</p>


이렇게, 안녕하세요는 왼쪽정렬, 작성자는 오른쪽 정렬이 된다.


{% capture some_var %}
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="codelion.css">
  </head>
  <body>
        <p class="text1">안녕하세요</p>
        <p class="text2">작성자</p>
        <p>저는 html과 css를 배우고 있습니다.</p>
  </body>
</html>
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

하지만, 아래에 문장을 하나 추가했을때,

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017192457676.png" alt="image-20221017192457676" style="zoom:80%;" />
</p>

위와 같이 출력이 이상하게 될 수 있다. 이런 경우는 , float 속성을 부여한 태그들을 묶어서 `overflow : hidden;` 속성을 부여해주면 된다.


{% capture some_var %}
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="codelion.css">
  </head>
  <body>
      <section class = "section1">
        <p class="text1">안녕하세요</p>
        <p class="text2">작성자</p>
      </section>
        <p>저는 html과 css를 배우고 있습니다.</p>
  </body>
</html>
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

{% capture some_var %}
```css
.text1 {
    float: left;
}

.text2 {
    float: right;
}

.section1{
    overflow: hidden;
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


위와 같이 section으로 묶고, overflow 속성을 부여하면


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221017192713097.png" alt="image-20221017192713097" style="zoom:80%;" />
</p>


정상적으로 출력이 되는 것을 확인할 수 있다.



---



## 하이퍼링크 걸기

<br>

{% capture some_var %}
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="codelion.css">
  </head>
  <body>
        <a href ="http://www.naver.com/"> <p class="text1">네이버</p></a>
  </body>
</html>
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


위와 같이 `<a href = "주소">   </a>` a 태그에 href 로 주소를 참조하면 안에 있는 내용 or 이미지를 클릭했을 때, 해당 주소로 이동할 수 있다.

