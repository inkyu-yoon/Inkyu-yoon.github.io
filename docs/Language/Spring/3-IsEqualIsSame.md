---
layout: default
title: "isEqualTo 와 isSameAs 차이점"
nav_order: 3
parent : Spring
grand_parent: Language
permalink: docs/Language/Spring/IsEqualIsSame
---

# Assertions 의 isEqualTo 와 isSameAs 차이점 분석

<br>

`assertj`의 `Assertions` 클래스를 테스트 코드를 작성하면서 많이 사용한다.



특히 `isEqualTo` 와 `isSameAs`를 사용해서 테스트를 많이 진행한다.



언뜻 보면, 의미상 같은 동작을 하는 메서드 같지만 다르다.



나도 어떤 점이 다른지, 여러 테스트를 해봤고 덕분에 확실히 알게 되었다.







간단하게 말하면



`isEqualTo` 는 해당 값이 같은지를 확인하고, 객체가 비교 대상이 된다면 Java 문법의 `equals()` 메서드와 같은 기능을 한다.



`isSameAs` 는 참조 값이 같은지 확인한다.



```
Member member1 = new Member(1L, "memberA", Grade.VIP);
Member member2 = new Member(1L, "memberA", Grade.VIP);
//↑ member 1 과 member 2 는 같은 속성의 값을 갖지만, 다른 참조값을 갖고 있는 서로 다른 객체이다.
        
String a = "HI";
String b = new String("HI");
        
//Case 1
assertThat(member1).isSameAs(member2);
        
//Case 2
assertThat(member1).isEqualTo(member2);
        
//Case 3
assertThat(member1.getName()).isEqualTo(member2.getName());
        
//Case 4
asserThat(a).isEqualTo(b);
```



위와 같은 상황이 있다고 할때,



`Case 1` 과 `Case 2`에서 에러가 발생할 것이고



`Case 3` 의 경우는, 각 객체에서 `memberA` 라는 이름을 가져온 뒤, 비교를 하므로 통과될 것이다.



`Case 4` 의 경우 역시, Java의 `equals()` 메서드의 기능처럼, 같은 문자열을 갖고 있으므로 주소값은 다르지만, 통과할 것이다.

<br>

```
Member member1 = new Member(1L, "memberA", Grade.VIP);
memberService.join(member1);
        
Member member2 = memberService.findMember(1L);
//↑ member 1을 입력하고, member 1 이 가진 id(key) 1L을 통해서 초기화한 member2는 같은 객체이다.
        
assertThat(member1).isSameAs(member2);
assertThat(member1).isEqualTo(member2);
assertThat(member1.getName()).isEqualTo(member2.getName());
```



위와 같은 상황에서는 사실상 `member1` 과 `member2` 가 같은 객체이므로



모든 상황에서 에러가 발생하지 않을 것이다.





------

{: .note-title }
> 정리
>
> 즉, 특정 값을 비교하거나 `String` 값 비교에는 `isEqualTo` 를 사용하고, 객체의 참조 값(주소)를 비교할 때에는 `isSameAs` 를 사용한다!