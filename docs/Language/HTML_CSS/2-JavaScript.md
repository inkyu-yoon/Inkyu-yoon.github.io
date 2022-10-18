---
layout: default
title: "JavaScript 기초"
nav_order: 2
parent : FrontEnd
grand_parent: Language
permalink: docs/Language/HTML_CSS/JavaScript
---

# JavaScript 기초

<br>

## JavaScript 의 기본



JavaScript 코드는 보통 `<body>`  태그 안에 넣고, `<script>` 태그로 감싸야 인식이 된다.

{% capture some_var %}
```html
<body>
	<script>
	
	</script>
</body>
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}



<br>



`.js` 파일을 `css` 파일을 만들었듯이, 생성한 뒤 HTML에 적용시키기 위해서는, 예를 들어 `test.js` 파일이 있다고 할때, 이 파일 안의 내용을 적용시키길 원하는 부분에 `<script src = "test.js"> </script>` 를 입력하면 된다.



{% capture some_var %}
```html
<body>
	<script src = "test.js">	</script>
</body>
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}



<br>



JavaScript에서 변수 선언은 다음과 같이 한다.


```
<script>
var name = '김철수';
var num = 1;
var power = false;
var num2 = 1.2;
var array = [1,2,3,4,5,6];

document.write(typeof name);
//typeof 는 변수의 데이터 형식을 반환한다. (String,int,float,bool 등등)

array.push(7);
</script>

```


`doucument.write()` 는 원하는 문자열을 출력해주는 메서드이다. HTML 의 `<p>` 태그와 유사한 기능이다.



`document.write("안녕")`  와 같이 사용하면 된다.



<br>



`.push()` 는 배열 마지막 값을 추가하는 메서드이다. 자바 문법에서 list 클래스의 `add`와 유사한 기능이다.



<br>



그리고, JavaScript는 배열에 `.indexOf()` 매서드를 사용할 수 있다.

또한`.sort()` 메서드를 배열에 사용하여 정렬할 수 있고,



<br>



`.sort((a,b)=>a-b)` 와 같이 자바의 람다식과 비슷한 방법으로 조건을 추가할수 있다.

안에 있는 조건식이 양수면 위치를 바꾸는 자바의 Comparator 와 유사하다.