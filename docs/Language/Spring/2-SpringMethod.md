---
layout: default
title: "· Getter and Setter / Optional.ofNullable / Assertions / AfterEach / assertThrows"
nav_order: 2
parent : Spring
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Spring/SpringMethod
---

# Getter and Setter / Optional.ofNullable / Assertions / Extract Method 생성 / AfterEach / assertThrows
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 클래스 인스턴스 변수 입력 및 출력 메서드 구현하기

<br>


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180104514.png" alt="image-20221018180104514" style="zoom:80%;" />
</p>

위와 같은 `Member` 클래스가 있을 때, 각 인스턴스 변수에 데이터 값을 저장하고 반환하는 `get` 과 `set` 메서드를 구현하는 방법은 매우 간단하다.



윈도우 기준 인텔리 제이 단축키 `ctrl + shift + n` 을 입력하고 `Action` 부분에서 `getter and setter` 를 검색하면 된다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180156346.png" alt="image-20221018180156346"  />
</p>


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180217568.png" alt="image-20221018180217568" style="zoom:80%;" />
</p>

get, set 메서드를 생성할 변수를 선택한 후 ok 버튼을 누르면


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180234985.png" alt="image-20221018180234985" style="zoom:80%;" />
</p>



이와 같이 자동으로 생성된다!





------

## Optional 을 사용하는 이유

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180301802.png" alt="image-20221018180301802" style="zoom:80%;" />
</p>

먼저, 회원 관리를 위한 클래스를 관리하기 위한 메서드들이 있는 인터페이스를 생성한다.



`findById()` 와 `findByName()` 메서드는 `Optional<Member>` 로 반환이 되게끔 작성이 되어있다.



`Optional<T>` 는 지네릭 클래스로 'T 타입의 객체'를 감싸는 래퍼 클래스이다.

`Optional` 타입의 객체에는 모든 타입의 참조변수를 담을 수 있다.



**이렇게 하면, 최종 연산의 결과를 `Optional` 객체에 담아서 반환을 하는데, 이처럼 객체에 담아서 반환을 하면 반환된 결과가 null인지 매번 if문으로 체크하는 대신**



**`Optional`에 정의된 메서드를 통해서 간단히 처리할 수 있어 에러가 발생하지 않는 간결하고 안전한 코드를 작성할 수 있다.**



즉, 간접적으로 `Null`을 다룰 수 있다.



구현부를 살펴보자.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180316899.png" alt="image-20221018180316899"  />
</p>



반환할 때, 그냥 `return store.get(id);` 였다면 null 값이 반환되었을 때 에러가 발생할 수 있다.



하지만, 위 코드와 같이 `Optional<Member>` 로 반환되는 경우에는 에러가 발생하지 않는다.



**`Optional.ofNullable()` 메서드를 활용해야한다.**



`findById` 는 `id`를 통해서 `Member` 객체를 꺼내오는 메서드이고 `store` 의 경우 `id`가 키값으로 지정되어있으므로



`store.get(id)` 를 통해서 해당 `id`에 속하는 `member` 객체를 불러오고 이를 `Optional.ofNullable`로 감싸주면 된다!


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180338387.png" alt="image-20221018180338387"  />
</p>



`findByName`의 경우 약간은 복잡하다.



먼저 `store.values()`를 통해 `store`에 저장된 모든 `member` 객체를 불러온다.



그 다음, `filter()` 메서드를 쓰기 위해서 `.stream()` 을 붙여준다.



`.filter(조건 람다식)` 은 해당 조건을 만족하지 않는 것들을 걸러준다.

`.filter(member -> member.getName().equals(name))` 을 통해서 `store`에 저장된 `member`의 `name` 들 중 입력한 name과 같은 member만 남게된다.



`.findAny()` 는 `stream`을 통해 걸러진 값을 아무거나 반환하는데, 
위와 같은 경우 중복된 이름이 없다고 가정되어 있으므로, 입력한 `name`을 가진 `member`가 있다면 반환할 것이다.



------

## Test Case 작성

<br>

윈도우 인텔리 제이의 경우



테스트 케이스를 작성하고 싶은 class의 이름을 클릭한 후 `ctrl + shift + t` 를 입력하면 쉽게 test 소스파일을 생성할 수 있다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180424603.png" alt="image-20221018180424603" style="zoom:80%;" />
</p>

create New Test 를 클릭하면 된다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180439521.png" alt="image-20221018180439521" style="zoom:80%;" />
</p>

그리고 테스트를 해볼 메서드를 선택한 후 ok 버튼을 누르면 소스파일이 생성된다.




------



테스트 파일 작성 시 `Assertions` 를 많이 사용하게 된다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180511334.png" alt="image-20221018180511334"  />
</p>

`Assertions`의 유용한 메서드를 사용하려면 import 시켜야 하는데, `junit`이 아닌 `assertj` 를 import해야한다!


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180530350.png" alt="image-20221018180530350" style="zoom:80%;" />
</p>

`Member result = repository.findById(member.getId()).get();`



에서 마지막에 `get()`이 붙는 이유는, `findById`의 반환값이 `optional`로 감싸져 있기 때문에, `get()` 메서드를 통해 객체만 꺼낼 수 있다.



`Assertions.assertThat(객체1).isEqualTo(객체2);`



을 통해서 객체 1과 객체 2 가 같은지 확인할 수 있다.







------

## AfterEach

<br>

테스트를 하는 과정에서 여러 메서드를 한번에 테스트 하다보면, 데이터가 겹쳐서 다른 메서드 테스트에 영향을 줄 수 있다. 그러므로 테스트 메서드가 끝날 때마다 메모리를 비워주는 것이 중요하다.



이 역할을 할 수 있는 에너테이션이 `@AfterEach`이다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180601104.png" alt="image-20221018180601104"  />
</p>

`clearStrore()` 메서드 안에는 간단하게 컬랙션 내 데이터를 모두 지워주는 `store.clear();` 를 구현하면 된다.





------

## Extract Method 생성

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180622550.png" alt="image-20221018180622550" style="zoom:80%;" />
</p>

`.ifPresent()` 를 통해서 만약 저장하려는 `member`의 `name`값이 이미 존재하는 `member`가 `repository`에 이미 존재한다면,



`IllegalStateException`이라는 에러를 일으키고 "이미 존재하는 회원입니다." 라는 에러메세지를 띄우게끔 구현하였다.







또한, 위 코드에서 중복회원은 가입이 안되도록 하는 부분을 메서드로써 따로 추출하는 단축키가 있다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180636071.png" alt="image-20221018180636071" style="zoom:80%;" />
</p>



해당 부분을 드래그하고, 윈도우 기준 `ctrl + alt + shift + t` 를 동시에 누르면 위와 같은 창이 나오고



Extract Method를 클릭하고 메서드 이름을 지정해주면 된다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180711445.png" alt="image-20221018180711445" style="zoom:80%;" />
</p>



편하게 메서드로서 추출해내었다.



------

## assertThrows

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018180728385.png" alt="image-20221018180728385" style="zoom:80%;" />
</p>



이전에, 중복된 회원이 있다면, `IllegalStateException` 에러를 일으키고 "이미 존재하는 회원입니다." 라는 에러메세지를 띄우게끔 구현하었다.



**`assertThrows`를 사용하면 try-catch 문의 역할을 간결하게 할 수 있다.**



먼저 `IllegalStateException e = assertThrows(IllegalStateException .class, () -> memberService.join(member2))` 를 살펴보자.



`member1`과 `member2`의 이름은 동일하고 `member1`은 이미 등록이 되어있는 상황이다.



`assertThrows(에러,조건식) `은 조건식이 실행되었을 때 에러가 발생하는지 확인해준다.





즉, 위의 경우에는` ()-> memberService.join(member2)` 람다식이 실행되었을때



`IllegalStateException `에러가 발생하는지 확인해준다.



에러는 `e`에 저장이 되고` getMessage()`를 통해 에러메세지를 추출할 수 있고



이를 통해서 중복된 회원이 가입을 시도할 경우, 에러가 발생하는지 안하는지 확인을 할 수 있다.