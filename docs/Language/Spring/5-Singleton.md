---
layout: default
title: "· 싱글톤 방식과 컴포넌트 스캔"
nav_order: 5
parent : Spring
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Spring/Singleton
---

# 싱글톤 방식과 컴포넌트 스캔 (Singleton, @ComponentScan, @AutoWired, @RequiredArgsConstructor,  @Primary)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 싱글톤의 존재 이유

<br>

- 웹 애플리케이션은 보통 여러 고객이 동시에 요청한다. 여러 고객이 수백 수천번 객체를 요청할때마다 새로 생성하면 메모리 낭비가 심해진다. 


이러한 문제를 해결하기위한 것이 객체를 딱 1 개 생성하고 공유하도록 설계하면 된다. 이것을 싱글톤 패턴이라고 한다.



------

## 싱글톤 패턴의 예시 중 하나

<br>

- `Car` 클래스가 있다고 가정해보자.

1. `private static final Car instance = new Car();`

   `static` 변수로 자기 자신을 `private` 접근 제한자와 `final` 로 생성



2. `getInstance()` 메서드를 사용하여 외부에서 `Instance`를 사용할 수 있게 만든다.



3. `private Car(){}` 객체의 기본생성자를 `private` 접근 제한자로 만들어두어, 외부에서 객체를 생성하지 못하게 막는다.


{% capture some_var %}
```java
//예시

public class Car{

    private static final Car instance = new Car();


    private Car(){}
    //외부에서 Car car = new Car(); 를 입력했을때, 기본 생성자가 private 라서 컴파일 오류가 생길 것

    public static Car getInstance(){
        return instance;
    }

    //Car 객체는 getInstance로 밖에 얻을 수 없고, 어디서 호출하더라도 같은 객체일 것

}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

위와 같은 경우는, 직접 싱글톤을 구현한 경우이고, 이렇게 구현한 싱글톤은 장점도 있지만 그만큼 단점도 존재한다.



하지만, 스프링 프레임워크는 싱글톤의 장점만 사용하게 해준다.



스프링 빈 저장소에 등록되는 객체들은 자동으로 싱글톤으로 관리된다.



`getBean()` 메서드를 사용해서 호출하고 `assertThat().isSameAs()` 를 사용해보면 같음을 확인할 수 있다.


<br>


싱글톤 방식을 사용할때에는 외부에서 클래스 맴버를 임의로 바꿀 수 없게 해야한다.



왜냐하면, 여러 사람이 같은 인스턴스를 사용하기 때문에, 외부에서 맴버 변수 값을 바꿀 수 있게 설계된 경우 데이터가 섞일 수 있다.





스프링 빈에 객체를 등록하기 위한 방법으로 클래스 앞에 `@Configuration` 에너테이션을 붙이고 `@Bean` 에너테이션을 통해 스프링 저장소에 빈을 등록했었다. (참고로 `@Configuration` 에너테이션이 없어도 빈 등록을 할 수 있지만, 싱글톤 방식이 깨져버린다.)





빈에 등록해야하는 객체의 수가 적다면, 직접 `@Bean` 에너테이션을 붙이는게 별거 아닐 수 있지만, 몇백개가 넘어가기 시작하면 번거로운 일이 된다.



이런 문제를 해결하기 위해 자동으로 스프링빈을 등록해주는 에너테이션이 있다.



------

## @ComponentScan의 작동 방식과 @Component 등록

<br>

1. 사용할 구현클래스 앞에 `@Component` 에너테이션을 붙인다. (참고로 `@Configuration` 에너테이션은 `@Component` 에너테이션을 포함한다.)

   -> 기존에는 Config 클래스를 따로 만들었지만, `@Component` 에너테이션은 구현 클래스 앞에 붙이는게 특징이다.

<br>


2. `@Autowired` 에너테이션을 의존관계, 즉 생성자 메서드 앞에 붙여준다. (매개변수를 받게끔 오버로딩 되어있는 경우가 아니라면 생략해도 된다.)

   원래는, Config 클래스에서 직접 의존관계를 지정해주었었다.

   구현 클래스 안에서 생성하는 객체들은 모두 추상화 시켜서 사용해도 , 구현 클래스들을 `@Component` 로 등록하기 때문에 자동으로 주입해준다. 

<br>

3. Lombok 라이브러리를 사용하면 `@RequiredArgsConstructor` 에너테이션을 사용할 수 있는데, 이 경우에는 기본생성자와 `@AutoWired`를 생략할 수 있다. (프로젝트 빌드 시 라이브러리 추가하자)

   예시는 아래와 같다.

{% capture some_var %}
```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {
     private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

//    @AutoWired
//  public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
//         this.memberRepository = memberRepository;
//         this.discountPolicy = discountPolicy;
//     }     
// @RequiredArgsConstructor 가 있어 생략 가능

}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


1. `@Component` 로 등록한 빈은 클래스명(제일 앞 문자를 소문자로 변경)으로 저장된다. (이전에는 메서드명이었다.)

   -> 컴포넌트 에너테이션을 통해, 빈 이름을 직접 등록해줄 수도 있다. 


2. 이전에 만들었던 `Config` 클래스는 의존관계 주입을 했었지만, `@ComponentScan` 방식은 간단하게 구성할 수 있다.



```
@Configuration
@ComponentScan
public class ComponentConfig{

}
```



위와 같은 `Config` 객체를 만든 뒤, `new AnnotationConfigApplicationContext(ComponentConfig.class);` 해주면 된다.





그러면, `@ComponentScan` 이 있는 `Config` 파일은 해당 파일이 있는 패키지를 포함하여, 하위 패키지에 있는 소스파일들을 탐색하여 `@Component` 를 기록한다.



따라서, `Config` 파일은 프로젝트의 최 상단에 있는것이 좋다.

------

{: .note-title }
> 마지막 의문
>
> 구현 클래스에서 잔뜩 추상화를 시켜놨는데, 추상화된 객체를 구현한 클래스가 여러개이고, 그 객체들이 스프링 빈에 등록되어 있는 경우 어떻게 의존관계를 자동으로 주입해줄까?
>
> -> 우선으로 주입하길 원하는 구현 클래스 앞에 `@Primary` 를 붙여서 해결하자!