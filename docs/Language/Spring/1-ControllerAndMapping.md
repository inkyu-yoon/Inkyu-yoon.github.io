---
layout: post
title: "· @Controller 와 @GetMapping"
nav_order: 1
parent : Spring
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Spring/ControllerAndMapping
---

# @Controller 와 @GetMapping을 이용해서 html파일 띄우기

<br>



스프링부트는 static 폴더에 `index.html` 파일을 생성하면, `index.html` 파일을 초기 화면으로 띄어준다.



그리고 `index.html` 파일의 `<body>` 부분에



`<a href = "/randomname">Click</a>` 를 입력해주고 (randomname 부분에는 원하는 이름을 넣는다.)



, 웹서버를 시작하고 localhost에 들어가보면,


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018175255522.png" alt="image-20221018175255522" style="zoom:80%;" />
</p>

Click이라는 하이퍼링크가 생성되고 클릭하면



http://localhost:8080/randomname 으로 이동하게 된다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018175313796.png" alt="image-20221018175313796" style="zoom:80%;" />
</p>



하지만, 아직 작성해 놓은게 없기 때문에, error page가 나온다.



------



먼저 `templates` 폴더에 `randomname.html` 파일을 만든다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018175333496.png" alt="image-20221018175333496" style="zoom:80%;" />
</p>



body 부분에 `This is randomname html` 이라는 문구를 작성하였다.



이제 이 `randomname.html` 파일을 띄워주기 위해 `Controller`라는 것을 만들어야 한다.



------


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018175353342.png" alt="image-20221018175353342"  />
</p>

java 폴더에 `MakeController` 라는 java파일을 생성했다.



`@Controller` 에너테이션을 꼭 해주어야 한다.



그리고



`@GetMapping("randomname")` 을 해주어야 `randomname` 메서드를 실행시켜준다.



사실 `GetMapping()` 기준으로 메서드가 실행되는 것이기 때문에, 메서드의 이름은 꼭 `randomname`이 아니여도 된다.



대신 return 값은 꼭 연결할 html파일명으로 해야한다.



randomname 메서드의 내용은 "randomname" 을 반환하는 간단한 코드를 작성하였다.



randomname 을 반환하기 때문에, randomname 이라는 이름의 html 파일이 있나 확인을 해보고 있다면 html 파일을 불러오게 되는 것이다.



------

서버를 다시 시작하고 Click을 눌러주면


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018175409072.png" alt="image-20221018175409072" style="zoom:80%;" />
</p>

이와 같이, `randomname.html` body 부분에 입력했던 문구가 나오는 것을 확인할 수 있다.



------

{: .note-title }
> 정리
>
> 1. <a href="/**원하는이름**"> Click</a>를 사용하던, url 을 입력해서 localhost:8080/**원하는이름** 으로 들어가던, html파일을 띄우고 싶다면 먼저, resource/templates 폴더에 [html이름].html 파일을 생성한다. 참고로 **원하는이름**은 GetMapping으로 넣을 인자와 동일해야 시스템이 캐치할 수 있다. 즉, GetMapping("**원하는이름**") 과 동일해야 캐치할 수 있다.
>
> 
> 2. html 파일을 생성했다면, 이를 연결시켜줄 Controller 자바 파일을 생성한다.
>
> 
> 3. @Controller 에너테이션을 넣어주어야 한다.
>
> 
> 4. @GetMapping("**원하는이름**") 을 작성하고 return 값으로 "[html이름]" 을 반환한다.
>
> 
> 5. [html이름]이 반환되면서, resource/templates 폴더에서 html을 찾고, 작성해둔, [html이름].html 이 띄워진다.
>
>
> ※ templates 폴더에 꼭 저장을 해주어야 controller가 찾을 수 있다. 만약 static 폴더에 html파일을 저장했다면, Controller가 아닌 index.html에서 참조를 <a href = "[html이름].html"> 로 해주면 바로 이동할 수 있다. 하지만 static 폴더에 저장한 html은 컨트롤러로 연결시켜줄 수 없다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018175430151.png" alt="image-20221018175430151" style="zoom:80%;" />
</p>
