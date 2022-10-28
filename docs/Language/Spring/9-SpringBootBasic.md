---
layout: default
title: "· API 활용 에너테이션 정리"
nav_order: 9
parent : Spring
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Spring/SpringBootBasic
---

# API 활용 에너테이션 정리
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## @RestController

<br>

`@Controller` 에 `@ResponseBody` 가 결합된 에너테이션이다.

-> 컨트롤러에 `@ResponseBody`를 안붙여도 문자열,JSON 등 전송한다.

```java
@Controller
@ResponseBody
public class Controoler{
    
    .....

}
```

```java
@RestController
public class Controoler{
    
   .....
    
}
```

위 두 코드는 같은 기능을 한다.

<br>

---

## @ResponseBody

<br>

- HTTP 요청 본문(request body)을 객체로 저장한다.

- `return` 값으로 반환하는 문자열을 표시해준다.

- 객체를 전달하면, 자동 매핑하여 JSON의 형태로 전달한다.

  ```java
  class User{
      private String id;
      private String name;
      private String password;
  }
  
  @Controller
  public class Controller{
      
      @ResponseBody
      @GetMapping("/test")
      public String test(){
          return "HI";
      }
      @ResponseBody
      @GetMapping("/test2")
      public User test2(){
          User user = new User("1234", "김철수", "asdf123");
          return user;
      }
  }
  ```
<p align="center">
  <img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221028162610950.png" alt="image-20221028162610950" style="zoom:67%;" />
</p>

  -> 문자열 반환시, 문자열이 그대로 출력
<p align="center">
  <img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221028162639974.png" alt="image-20221028162639974" style="zoom: 80%;" />
</p>

  -> 객체 반환시, 객체의 맴버들이 자동 매핑되어 JSON 형태로 출력



- **HTTP 요청 body를 자바 객체로 변환하고, 자바 객체를 다시 HTTP 응답 body로 변환해준다.** html 태그가 없는 데이터만을 표시해준다.

<br>

---

## @RequestMapping

<br>

- 클래스, 메서드에 사용가능하고, 클래스에 사용시, GetMapping, PostMapping,DeleteMapping, PutMapping 등등과 사용할때 좋다.

```java
@RestController
public class Controller{
    @GetMapping("/users/get")
    public String get(){}
    
    @GetMapping("/users/post")
    public String post(){}
    
    @GetMapping("/users/delete")
    public String delete(){}
}
```

위 코드를 보면, 각 Mapping 에너테이션에 `/users/` 가 공통으로 사용되어 있다. 이를 `@RequestMapping` 을 사용하면

```java
@RestController
@RequestMapping("/users")
public class Controller{
    @GetMapping("/get")
    public String get(){}
    
    @GetMapping("/post")
    public String post(){}
    
    @GetMapping("/delete")
    public String delete(){}
}
```

위와 같이, 공통으로 적용시킬 수 있다.

<br>

---

## @PathVariable

- 매개변수에 `@PathVariable` 에너테이션을 사용하면, url로부터 변수를 입력받아, 메서드 안에서 사용할 수 있다.

```java
@RestController
@RequestMapping("/api/v1/users")
public class Controller{
     @GetMapping("/get/{input}")
    public String get(@PathVariable String input){
        return input+"는 url로 부터 입력받았습니다.";
    }
}
```

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221028170142000.png" alt="image-20221028170142000" style="zoom:80%;" />

위와 같이 url로부터 입력받은 input을 변수로서 메서드에서 활용하여 위와 같은 문구를 출력하였다.

참고로, RestController 나 ResponseBody가 없다면 사용이 안된다.



<br>

---



## @RequestParam

<br>

- @PathVariable과 비슷하나, url 로 부터 쿼리문을 전달받을 수 있다.

```java
@RestController
@RequestMapping("/api/v1/users")
public class Controller{
    
    @GetMapping("/rp-test")
    public String test(@RequestParam String rp1) {
       return "입력받은 데이터는 "+ rp1;
    }
}
```

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221028171224958.png" alt="image-20221028171224958" style="zoom:80%;" />
</p>

url을 작성할때는, `?` 로 쿼리문의 시작을 표시하고, `@RequestParam`으로 지정한 변수명에 값을 입력하면 된다.

`http://localhost:8080/api/v1/users/rp-test?rp1=hello` 와 같이 작성할 수 있다.



<br>

---

## @RequestBody

<br>

- body 구현부로부터 데이터를 입력받을 수 있으며, 보통 Map의 형태로 사용한다.

```java
@RestController
@RequestMapping("/api/v1/users")
public class Controller{
    
    @PostMapping("/rb-test")
    public User test(@RequestBody Map<String, String> postData) {
        User user = new User(postData.get("id"), postData.get("name"), postData.get("password"));
        return user;
    }
}
```

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221028170808402.png" alt="image-20221028170808402" style="zoom: 80%;" />
</p>


- body 에 위와 같이 입력되어 있다면, body 내용으로부터 데이터를 입력받아 User 객체를 생성할 수 있다.

```java
@RestController
@RequestMapping("/api/v1/users")
public class Controller{
    
    @GetMapping("request2")
    public String getRP2(@RequestParam Map<String, String> RP) {
        StringBuilder sb = new StringBuilder();
        RP.entrySet().forEach(map -> {
            sb.append(map.getKey() + " : " + map.getValue() + "\n");
        });

        return sb.toString();
    }
}
```

위와 같이 코드를 작성하면, url에 입력될 변수명을 몰라도, 입력할 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221028171636714.png" alt="image-20221028171636714" style="zoom:80%;" />
</p>

`http://localhost:8080/api/v1/get-api/request2?random=hi&random2=hello&random3=bye` 와 같이 입력하면 위와 같은 출력화면을 볼 수 있다.