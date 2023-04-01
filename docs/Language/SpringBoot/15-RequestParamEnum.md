---
layout: post
title: "· @RequestParam 다이렉트로 Enum 타입으로 매핑하기"
nav_order: 15
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/RequestParamEnum
---

# @RequestParam 다이렉트로 Enum 타입으로 매핑하기

<br>

***Post***필드에는 ***Enum***으로 관리되는 ***Category***필드가 있었습니다.

그리고 저는 카테고리를 Request Parameter로 입력받아 카테고리에 해당하는 게시글들을 반환하는 GET 요청 API를 만들려고 했습니다.

예상하는 uri는 `/api/v1/posts?category=qna` 와 같을 것입니다.

```java
@RestController
@Slf4j
@RequestMapping("/api/v1/posts")
@RequiredArgsConstructor
public class PostApiController {

    @GetMapping
    public ResponseEntity<Response<Page<PostGetListResponse>>> getAllByCategory(@RequestParam String requestCategory, Pageable pageable){
        Category category = Category.valueOf(requestCategory)
        Page<PostGetListResponse> response = postService.getAllPostsByCategory(category, pageable);
        return ResponseEntity.ok(Response.success(response));
    }
}

```

따라서 위 코드와 같이 ***RequestParam***은 ***String***값으로 입력받고, 입력받은 값을 ***Enum***타입으로 변환시켜야 했습니다.

코드가 많이 추가되어 길어지는 것은 아니지만, 입력받음과 동시에 처리된다면 코드 가독성 향상과 핵심 로직만 메서드 안에 담을 수 있을 것이라 생각되어 방법을 찾아봤습니다.

<br>

```java
public enum Category {
    QNA, COMMUNITY, STUDY, NOTICE;

    public static Category create(String requestCategory) {
        for (Category value : Category.values()) {
            if (value.toString().equals(requestCategory)) {
                return value;
            }
        }
        throw new IllegalStateException("일치하는 카테고리가 존재하지 않습니다.");
    }
}

```

먼저 ***Enum***타입인 ***Category***안에 ***String***타입인 ***requestCategory***를 ***Category***로 만들어주는 메서드를 정의합니다.

<br>

```java
public class PostEnumConverter implements Converter<String, Category> {

    @Override
    public Category convert(String requestCategory) {
        return Category.create(requestCategory.toUpperCase());
    }
}
```

그리고 `import org.springframework.core.convert.converter.Converter;` 에 있는 ***Converter*** 의 ***convert*** 메서드를 오버라이딩합니다.

Request Parameter로 입력되는 문자열을 ***toUpperCase***를 이용해서 모든 문자를 대문자로 바꿔준 후 넘겨줍니다.

<br>

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverter(new PostEnumConverter());
    }
}
```

그리고 ***WebConfiguer***의 ***addFormatters*** 메서드를 오버라이딩하여 만든 ***Converter***를 추가해줍니다.

이제 잘 동작하는지 확인을 해봅니다.

`/api/v1/posts?category=QNA` 나 `/api/v1/posts?category=qna` 로 요청했을 때, 잘 동작하는 것이 확인이 되지만,

`/api/v1/posts?category=` 라던지 `/api/v1/posts?category=hi` 와 같은 잘못된 요청 시, 에러 핸들링이 되지 않음을 확인했습니다.

<br>

로그를 보니 `/api/v1/posts?category=`  요청처럼 존재하지 않는 문자열로 요청을 하는 경우에는 ***MissingServletRequestParameterException*** 에러가,

`/api/v1/posts?category=hi`  요청처럼 ***Enum*** 으로 존재하지 않는 값을 요청하는 경우에는 ***MethodArgumentTypeMismatchException*** 에러가 발생하는 것을 확인했습니다.

```java
@RestControllerAdvice
public class ExceptionManager {

    @ExceptionHandler(MissingServletRequestParameterException.class)
    public ResponseEntity<?> converterExceptionHandler(MissingServletRequestParameterException e) {
        return ResponseEntity.status(HttpStatus.BAD_REQUEST)
                .body(Response.error(ErrorCode.REQUEST_PARAM_NOT_MATCH.getMessage()));
    }
    @ExceptionHandler(MethodArgumentTypeMismatchException.class)
    public ResponseEntity<?> converterSecondExceptionHandler(MethodArgumentTypeMismatchException e) {
        return ResponseEntity.status(HttpStatus.BAD_REQUEST)
                .body(Response.error(ErrorCode.REQUEST_PARAM_NOT_MATCH.getMessage()));
    }
}
```

따라서 위와 같이 에러를 핸들링하는 ***ExceptionManager*** 를 통해 에러 처리를 진행했습니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230401165622083.png" alt="image-20230401165622083" style="zoom: 50%;" />
</p>

정상적으로 에러 응답을 확인할 수 있게 되었습니다.

