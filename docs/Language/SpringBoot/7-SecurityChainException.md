---
layout: post
title: "· Security Chain antMatchers 관련 Exception Handling"
nav_order: 7
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/SecurityChainException
---

# Security Chain antMatchers 관련 Exception Handling
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

Security Chain 설정에서, 

특정 Role만 Url 요청을 접근할 수 있도록 설정할때 (`.hasRole()` 되어있는)  혹은 `.authenticated()` 되어있는 url에 `AUTHORIZATION` 내용이 아예 없는 경우

Chain에서 발생하는 예외를 처리하기 위해서는 `AccessDeniedHandler` 와 `AuthenticationEntryPoint` 를 상속받아 구현해야한다.

예시로 아래와 같이 구현할 수 있다.

<br>

---



## Error Response 구성

<br>



```java
 public static void setErrorResponse(HttpServletResponse response, ErrorCode errorCode) throws IOException {

        // 4에러 응답코드 설정
        response.setStatus(errorCode.getHttpStatus().value());
        // 응답 body type JSON 타입으로 설정
        response.setContentType("application/json;charset=UTF-8");

        Response<ErrorDto> error = Response.error(new ErrorDto(errorCode.toString(), errorCode.getMessage()));

        //예외 발생 시 Error 내용을 JSON화 한 후 응답 body에 담아서 보낸다.
        Gson gson = new Gson();
        String responseBody = gson.toJson(error);

        response.getWriter().write(responseBody);
    }


@AllArgsConstructor
@Getter
public enum ErrorCode {
    
    FORBIDDEN_REQUEST(HttpStatus.FORBIDDEN, "ADMIN 회원만 접근할 수 있습니다."),
    INVALID_TOKEN(HttpStatus.UNAUTHORIZED, "유효하지 않은 토큰입니다.");
    
    private HttpStatus httpStatus;
    private String message;
}
```

<br>

먼저 위와 같은 메서드를 구현한다.

`HttpServletResponse` 에 `status` 와 `contentType` 를 설정하고

`.getWriter.write()` 메서드로 응답할 body를 JSON화 해서 응답할 내용을 설정하면 된다.

<br>

---

<br>

## AccessDeniedHandler 구현하기

<br>

```java
import likelion.sns.Exception.ErrorCode;
import likelion.sns.Exception.ExceptionManager;
import lombok.extern.slf4j.Slf4j;
import org.springframework.security.access.AccessDeniedException;
import org.springframework.security.web.access.AccessDeniedHandler;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
@Slf4j
public class CustomAccessDeniedHandler implements AccessDeniedHandler {

    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException {

        log.info("{}", accessDeniedException.getMessage());

        ErrorCode errorCode = ErrorCode.FORBIDDEN_REQUEST;

        ExceptionManager.setErrorResponse(response, errorCode);
    }
}
```

<br>

`handle` 을 오버라이딩한 후, 에러 발생시 응답할 코드를 설정하면 된다.

그러면 `.hasRole` 관련 예외를 핸들링할 수 있다.

<br>

---

<br>

## AuthenticationEntryPoint 구현하기

<br>

```java
import likelion.sns.Exception.ErrorCode;
import likelion.sns.Exception.ExceptionManager;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpHeaders;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.web.AuthenticationEntryPoint;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
@Slf4j
public class CustomAuthenticationEntryPointHandler implements AuthenticationEntryPoint {

    /**
     *  토큰 없이 Security Chain 에서 autenticated된 url 접근 시, 에러 핸들링
     */
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException {
        final String authorization = request.getHeader(HttpHeaders.AUTHORIZATION);

        if (authorization == null) {
            log.error("토큰이 존재하지 않습니다.");
            ErrorCode errorCode = ErrorCode.TOKEN_NOT_FOUND;

            ExceptionManager.setErrorResponse(response, errorCode);
        }
    }
}
```

<br>
`commence` 를 오버라이딩하면 되고, authorization이 null 인 조건문을 사용하여, 예외 처리를 하면 된다.

<br>

---
<br>

## 확인

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221228215114914.png" alt="image-20221228215114914" style="zoom:80%;" />
</p>

> 토큰 없이 요청할 경우

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221228214807777.png" alt="image-20221228214807777" style="zoom:80%;" />
</p>

> 권한 없는 사용자가 요청할 경우



