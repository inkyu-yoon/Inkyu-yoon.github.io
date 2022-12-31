---
layout: post
title: "· Controller 에서 요청 객체 변수 유효성 검사하기"
nav_order: 9
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/validation
---

# Controller 에서 요청 객체 변수 유효성 검사하기

<br>



<br>

## validation 라이브러리 추가

```
	implementation 'org.springframework.boot:spring-boot-starter-validation'
```

<br>

## Request 객체 생성



```java
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.ToString;

import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotEmpty;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Positive;

@Getter
@AllArgsConstructor
@NoArgsConstructor
public class UserRequest {


    @NotEmpty(message = "빈 문자열이나 null 은 허용되지 않습니다.")
    private String name;

    @NotBlank(message = "빈 문자열이나 null, 공백으로만 이루어진 것은 허용되지 않습니다.")
    private String password;

    @Positive(message = "양수만 허용됩니다.")
    private Long age;

    @NotNull(message = "null은 허용되지 않습니다.")
    private Long id;
}

```



위와 같이 `@NotBlank`, `@Positive` 등 적절한 에너테이션으로 유효성 조건을 추가할 수 있고, 원하는 default message도 추가해서 입력할 수 있다.



## Controller 설정

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/test")
public class testController {


    @PostMapping
    public ResponseEntity newUser(@Validated @RequestBody UserRequest userRequest, BindingResult br) {
        if (br.hasErrors()) {
            return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(br.getFieldError().getDefaultMessage());
        }
        return ResponseEntity.ok(userRequest);
    }
}
```



유효성 체크를 할 객체 앞에 `@Validated` 를 붙여주고, 파라미터로 `BindingResult` 를 추가하면, 에러 발생 시 핸들링도 할 수 있다.

<br>

## 테스트 해보기


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221231175234997.png" alt="image-20221231175234997" style="zoom: 67%;" />
</p>

> name 이 빈 문자열일 경우 에러 발생

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221231175322855.png" alt="image-20221231175322855" style="zoom: 67%;" />
</p>

> password의 경우는 `@NotBlank` 조건에 의해서 공백만 입력되어도 에러 발생

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221231175410469.png" alt="image-20221231175410469" style="zoom: 67%;" />
</p>

> Long 타입의 id 가 null 로 입력되어 에러 발생
> 
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221231175508042.png" alt="image-20221231175508042" style="zoom:67%;" />
</p>

> age 값을 `@Positive` 조건을 걸어서, 음수 입력 시 에러 발생