---
layout: post
title: "· Thymeleaf 기본 문법 정리"
nav_order: 5
parent : FrontEnd
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/FrontEnd/ThymeleafBasic
---

# Thymeleaf 기본 문법 정리
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


<br>

## 1. 텍스트 출력

<br>

- model로 넘긴 데이터 출력

```html
<p th:text="${data}"></p>
```

<br>

- 문자열에 html 태그가 포함되어있는 경우

```html
<p th:utext="${data}"></p
```

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230125011442336.png" alt="image-20230125011442336" style="zoom:80%;" />
</p>

> 그냥 `th : text` 를 사용하면 태그가 위의 경우처럼, 그냥 출력된다.
>
> 하지만 `th : utext` 를 사용하면, 태그가 적용된 상태로 출력된다.

<br>

## 2. 객체 및 컬렉션 멤버 출력하기

<br>

```html
<!--User라는 객체에 username, age 필드가 있는 경우-->

<p th:text="${user.username}"></p>
<p th:text="${user.age}"></p>

<!--List<User> 가 전달되었을 때-->

<p th:text="${userList[0].username}"></p>
<p th:text="${userList[0].age}"></p>

<!--Map<String,User> 가 전달되었을 때-->

<p th:text="${userMap['userA'].username}"></p>
<p th:text="${userMap['userA'].age}"></p>

<!--  지역 변수로 선언-->

<div th:with="person=${user}">
    <p th:text="${person.username}"></p>
</div>

<p th:text="${person.username}"></p> -> 지역변수를 선언한 태그 밖에서 사용하면 에러 발생
```



<br>

## 3. 세션 데이터 출력 (model에서 전달하는 것이 아님)

<br>

```html
<!-- session.setAttribute("sessionData", "세션 데이터"); -->
<!-- 세션 데이터, 접근 -->
<p th:text="${session.sessionData}"></p>
```

<br>

## 4. 날짜 출력

<br>

```html
<!-- model.addAttribute("localDateTime", LocalDateTime.now()); 로 넘어왔을 때-->

<ul>
    <li>default = <span th:text="${localDateTime}"></span></li>
<!--    default = 2023-01-25T23:59:43.527748100-->
    
    <li>yyyy-MM-dd HH:mm:ss = <span th:text="${#temporals.format(localDateTime,'yyyy-MM-dd HH:mm:ss')}"></span></li>
<!--    yyyy-MM-dd HH:mm:ss = 2023-01-25 23:59:43-->
    
    <li>${#temporals.day(localDateTime)} = <span th:text="${#temporals.day(localDateTime)}"></span></li>
<!--    ${#temporals.day(localDateTime)} = 25  (몇 일인지)-->
    
    <li>${#temporals.month(localDateTime)} = <span th:text="${#temporals.month(localDateTime)}"></span></li>
<!--    ${#temporals.month(localDateTime)} = 1  (몇 월인지)-->
    
    <li>${#temporals.monthName(localDateTime)} = <span th:text="${#temporals.monthName(localDateTime)}"></span></li>
<!--    ${#temporals.monthName(localDateTime)} = 1월 (몇 월인지 월까지)-->
    
    <li>${#temporals.year(localDateTime)} = <span th:text="${#temporals.year(localDateTime)}"></span></li>
<!--    ${#temporals.year(localDateTime)} = 2023 (년도)-->
    
    <li>${#temporals.dayOfWeek(localDateTime)} = <span th:text="${#temporals.dayOfWeek(localDateTime)}"></span></li>
<!--    ${#temporals.dayOfWeek(localDateTime)} = 3 (몇째주인지)-->
    
    <li>${#temporals.dayOfWeekName(localDateTime)} = <span th:text="${#temporals.dayOfWeekName(localDateTime)}"></span></li>
<!--    ${#temporals.dayOfWeekName(localDateTime)} = 수요일 (무슨요일인지 요일까지)-->
    
    <li>${#temporals.dayOfWeekNameShort(localDateTime)} = <span th:text="${#temporals.dayOfWeekNameShort(localDateTime)}"></span></li>
<!--    ${#temporals.dayOfWeekNameShort(localDateTime)} = 수 (무슨요일인지)-->
    
    <li>${#temporals.hour(localDateTime)} = <span th:text="${#temporals.hour(localDateTime)}"></span></li>
<!--    ${#temporals.hour(localDateTime)} = 23 (시간 24 단위로)-->
    
    <li>${#temporals.minute(localDateTime)} = <span th:text="${#temporals.minute(localDateTime)}"></span></li>
<!--    ${#temporals.minute(localDateTime)} = 59 (몇분인지) -->
    
    <li>${#temporals.second(localDateTime)} = <span th:text="${#temporals.second(localDateTime)}"></span></li>
<!--    ${#temporals.second(localDateTime)} = 43 (몇초인지)-->
    
    
</ul>
```

<br>

## 5. URL

<br>

```html
<!-- 쿼리 파라미터 url-->
<!-- 
model.addAttribute("param1", "data1");
model.addAttribute("param2", "data2");
-->

<a th:href="@{/(param1=${param1},param2=${param2})}">안녕</a>

<!-- 위 링크 클릭 시 /?param1=data1&param2=data2 경로로 이동 -->

<a th:href="@{/{param1}(param1=${param1},param2=${param2})}">안녕</a>

<!-- 위 링크 클릭 시 /data1?param2=data2 경로로 이동 -->

```

<br>

## 6. 조건식

<br>

```html
<a th:text="(10%2==0)?'짝수':'홀수'"></a>
<!-- 자바의 3항 조건식과 유사하다. -->

<a th:text="${nullData}?:'데이터가 없습니다.'"></a>
<!-- model로 넘겨진 데이터가 null이 아니면, 해당 데이터가 출력되고, null 이면 '데이터가 없습니다.' 가 출력 -->

<a th:text="${nullData}?:_">타임리프 태그 무효화</a>
<!-- 언더 스코어가 있으면, no-operation 되어, 타임리프 태그가 무효화 된다. -->

<div th:each="user : ${userList}">
    <span th:text="|'미성년자' ${user.username}|" th:if="${user.age <= 20}"></span>
</div>
<!-- if문을 만족해야만 태그가 유효해진다. -->


<div th:each="user : ${userList}">
    <td th:switch="${user.age}">
        <span th:case="10">10살</span>
        <span th:case="20">20살</span>
        <span th:case="*">기타</span>
    </td>
</div>
<!-- switch 문법과 유사하다. -->
```

<br>

## 7. 체크 처리

<br>

```html
<!-- 체크 처리-->

- checked o <input type="checkbox" name="active" th:checked="true" /><br/>
- checked x <input type="checkbox" name="active" th:checked="false" /><br/>
- checked=false <input type="checkbox" name="active" checked="false" /><br/>

<!-- 타임리프 구문이 없으면, false로 표시해도 디폴트가 항상 체크되어 있다.-->
```

<br>

## 8. 반복

<br>

```html
<!-- 컬렉션 형태로 userList가 넘어왔을 때-->
<div th:each="user : ${userList}">
    <p th:text="${user.age}"></p>
    <p th:text="${user.username }"></p>
</div>
```

<br>

## 9. 블록 태그

<br>

```html
<!-- 여러개의 태그를 제어하고 싶을 때 타임리프의 블록테그를 사용한다.-->
<th:block th:if="${session.MENU_LIST !=null}">
    <div th:each="testList : {session.test}">
        <div th:text="${testList.name}"></div>
    </div>
</th:block>
```

<br>

## 10. 자바 스크립트 인라인

<br>

```html
<!-- 스크립트에 변수로 할당할때는, [[${변수}]] 와 같은 형식으로 입력한다. -->
<script th:inline="javascript">
 var username = [[${user.username}]];
 var age = [[${user.age}]];
    
<!--자바스크립트 내추럴 템플릿 (주석 처리 된 것이 렌더링 된다.) -->
 var username2 = /*[[${user.username}]]*/ "test username";
    
<!--객체 (json 형태로 변경된다.) -->
 var user = [[${user}]];
    
</script>

```

<br>

## 11. 템플릿 조각

<br>

```html
<!-- copy 라는 이름dml fragment가 /templates/fragment/footer 위치에 있을 때-->
<footer th:fragment="copy">
 푸터
</footer>
```

<br>

```html
<!-- insert의 경우, 해당 문법이 있는 태그에 감싸진다.-->
<div th:insert="~{template/fragment/footer :: copy}"></div>

<!-- replace의 경우, 해당 문법이 있는 태그에 덮어씌워진다..-->
<div th:replace="~{template/fragment/footer :: copy}"></div>
<div th:replace="template/fragment/footer :: copy"></div>
```

<br>


## 12. 템플릿 조각

<br>

```html
<div th:each="region : ${regions}" class="form-check form-check-inline">
    <input type="checkbox" th:field="*{regions}" th:value="${region.key}"
           class="form-check-input">
    <label th:for="${#ids.prev('regions')}"
           th:text="${region.value}" class="form-check-label">서울</label>
</div>
```

루프로 타임리프 변수가 생성되는 경우, `th:field`를 사용하면 , 해당 블록의 id를 순차적으로 1부터 붙여서 명명해준다.

> `th:field` 로 명시하면, model 로 넘긴 객체의 변수명으로 id와 name을 생성해준다. 

<br>

```html
<input type="checkbox" value="SEOUL" class="form-check-input" id="regions1"
name="regions">
<input type="checkbox" value="BUSAN" class="form-check-input" id="regions2"
name="regions">
<input type="checkbox" value="JEJU" class="form-check-input" id="regions3"
name="regions">

```

위와 같은 결과가 나오고,

따라서, label과 같은 태그에 `${#ids.prev('regions')}` 를 붙여주면 동적으로 변하는 id 값을 반영한다.

<br>

## 13. ENUM 타입 사용

<br>

```html
<div th:each="type : ${T(hello.itemservice.domain.item.ItemType).values()}">
```

스프링 EL 문법으로 Enum 타입의 위치를 명시해서 사용한다.

<br>
