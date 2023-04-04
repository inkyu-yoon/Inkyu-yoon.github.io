---
layout: post
title: "· Request Dto에 있는 Enum 타입 필드 validation 예외처리 개선하기"
nav_order: 17
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/EnumValidation
---

# Request Dto에 있는 Enum 타입 필드 validation 예외처리 개선하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


<br>

```
implementation 'org.springframework.boot:spring-boot-starter-validation'
```



위 ***validation*** 라이브러리를 사용하면

Controller에서 ***@RequestBody*** 어노테이션으로 매핑하는 request dto의 필드 유효성을 검사할 수 있습니다.



```java
public class PostCreateRequest {
    @NotBlank(message = "제목은 필수 입력 항목입니다.")
    private String title;
    @NotBlank(message = "내용은 필수 입력 항목입니다.")
    private String content;
    @NotNull(message = "유효하지 않은 카테고리가 입력되었습니다.")
    private Category category;
}
```



위와 같이 매핑되는 필드에 ***validation***에서 제공하는 어노테이션을 사용하면 됩니다.

예를 들어, 위 ***Category*** 필드에 사용한 ***@NotNull*** 어노테이션의 경우 ***null***이 입력되는 것을 허용하지 않습니다.

<br>

```
Resolved [org.springframework.http.converter.HttpMessageNotReadableException: JSON parse error: Cannot deserialize value of type `com.growith.domain.post.Category` from String "category": not one of the values accepted for Enum class: [NOTICE, STUDY, QNA, COMMUNITY]
```

그리고 ***BindingError***를 확인하기 위해 테스트 코드를 작성한 뒤, 실행시켜보니 문제가 발생했습니다.

<br>

```java
public enum Category {
    QNA, COMMUNITY, STUDY, NOTICE;
    }
}
```

카테고리에는 다음과 같은 상수들이 존재하는데,

<br>

***null*** 값이 입력되는 상황은 처리할 수 있었지만, Enum으로 정의되지 않은 문자열(위 예시에선 category:"category")이 입력됐을 때는 처리가 되지 않은 상황이었습니다.

또한, "qna" 와 같이 소문자로 입력되는 경우에도 처리되지 않았습니다.

그래서 대소문자는 구별하지 않고 일치하면 정상적으로 로직이 실행되고,

null이거나 enum으로 정의하지 않은 값이 들어오는 경우만 예외처리가 되도록 구현하였습니다.

<br>

```java
public enum Category {
    QNA, COMMUNITY, STUDY, NOTICE;
    }

    @JsonCreator
    public static Category parsing(String inputValue) {
        return Stream.of(Category.values())
                .filter(category -> category.toString().equals(inputValue.toUpperCase()))
                .findFirst()
                .orElse(null);
    }
}
```

방법은 enum 객체 안에 ***@JsonCreator*** 어노테이션과 생성자를 구현하여 Json데이터를 역직렬화 하는 과정을 수동 설정하는 것입니다.

category 를 키값으로 하는 value값을 파라미터로서 ***inputValue***값으로 받아와서 로직이 수행됩니다.

입력되는 ***inputValue***값을 ***toUpperCase()*** 메서드로 대문자로 변환 후,

일치하는 Category가 있다면 반환하고 없다면 ***null***을 반환하도록 했습니다.

null이 반환되면 validation으로 설정한 ***@NotNull***어노테이션에 의해 ***BindingError***가 발생할 것입니다.

### 결과

<div align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230404192343858.png" alt="image-20230404192343858" style="zoom: 50%;" />
</div>

> 목표했던 대로 예외처리가 된 것을 확인할 수 있었고, 에러 응답을 통해 어떤 에러가 발생했는지 명시할 수 있게 되었습니다.
