---
layout: default
title: "스프링 빈 컨테이너(Bean Container)의 원리 "
nav_order: 4
parent : Spring
grand_parent: Language
permalink: docs/Language/Spring/SpringBean
---

# 스프링 빈 컨테이너(Bean Container)의 원리 (@Configuration , @Bean)

<br>

먼저, 스프링 컨테이너를 생성하고, 객체들을 스프링 컨테이너의 빈으로서 저장하려면,



```
ApplicationContext ac = new AnnotationConfigApplicationContext(Config.class);
```

<br>

`ApplicationContext` 인터페이스를 사용해서 `AnnotationConfigApplicationContext` 클래스를 생성하고,



매개변수로서 `@Configuration` 에너테이션이 붙은 클래스와 `@Bean` 에너테이션을 통해 빈 설정이 되어있는,



`Config` 클래스를 입력해야한다.



참고로, `ApplicationContext는` 빈을 조회하고 관리하는 기능이 있는 `BeanFactory` 인터페이스를 상속하고 있고,



단순히 빈을 조회하고 관리하는 기능 외에 부가기능을 사용하기 위해 `ApplicationContext` 인터페이스를 사용한다.



아무튼, 매개변수로서 입력받은 클래스를 바탕으로 스프링 컨테이너를 생성한다.



| **스프링 컨테이너**                                          |                        |
| ------------------------------------------------------------ | ---------------------- |
| **빈 이름**                                                  | **빈 객체**            |
| @Bean 표시된 메서드의 이름 (@Bean("name") 과 같이 직접 이름을 지정해줄 수도 있다.) | 메서드가 반환하는 객체 |



예를 들어


{% capture some_var %}
```java
@Configuration
class ExampleConfig{
	
    @Bean
    void Car car(){
		return new carImpl(oil(),engine());     
    }

    @Bean
    void Oil oil(){
		return	new oilImpl();   
    }
    
    @Bean("CustomName")
    void Engine engine(){
		return	new engineImpl();
    }

}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


위와 같은 @`Configuration` 과 @`Bean` 에너테이션으로 정보를 설정한 `ExampleConfig` 라는 클래스가 있다고할 때,



`ApplicationContext ac = new AnnotationConfigApplicationContext(ExampleConfig.class);` 를 하게 되면

<br>

| **스프링 컨테이너**                  |             |
| ------------------------------------ | ----------- |
| **빈 이름**                          | **빈 객체** |
| car                                  | carImpl     |
| oil                                  | oilImpl     |
| CustomName (이름을 지정해주었으므로) | engineImpl  |

<br>

위와 같은 스프링 컨테이너가 생성될 것이다.



그리고 `car` 클래스의 반환값의 매개변수에 추상화가 되어있지만, 스프링 컨테이너에서 등록된 빈 정보를 통해 구현객체가 입력될 수 있게끔 관리해준다.



즉, 의존관계가 연결이 된다.



`Xml` 파일로 등록된 빈을 확인하고 싶다면 `AnnotationConfigApplicationContext` 대신 `GenericXmlApplicationContext` 를 사용하면 된다.