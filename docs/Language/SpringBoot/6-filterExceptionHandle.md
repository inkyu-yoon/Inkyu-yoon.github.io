---
layout: post
title: "· Security Filter에서 발생하는 Exception 처리하기"
nav_order: 6
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/FilterExceptionHandle
---


# Security Filter에서 발생하는 Exception 처리하기

<br>
토큰이 만료되었거나, 유효하지 않은 값이 들어왔을 때, 예외처리를 해주려고 controller 나 sevice 단에서 예외를 던지는 방식을 고민했는데, 해결되지 않았고 해결되지 않는 이유가 있었다.

ExceptionManager 로 사용하는 `@RestControllerAdvice` 의 `@ControllerAdvice` 는 **Filter 단에서 발생한 예외를 핸들링 해주지 못한다.**


{: .highlight }
따라서, 새로운 Filter를 정의해서 Filter Chain에 추가해주어야 한다.

<br>

```java
import likelion.sns.jwt.ExceptionHandlerFilter;
import likelion.sns.jwt.JwtTokenFilter;
import likelion.sns.service.UserService;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

@EnableWebSecurity
@RequiredArgsConstructor
public class SecurityConfig {
    private final UserService userService;
    @Value("${jwt.token.secret}")
    private String secretKey;
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) throws Exception {
        return httpSecurity
                .httpBasic().disable()
                .csrf().disable()
                .cors().and()
                .authorizeRequests()
                .antMatchers("api/v1/users/join", "/api/v1/users/login").permitAll()
                .antMatchers(HttpMethod.POST, "api/v1/**").authenticated()
                .and()
                .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .addFilterBefore(new JwtTokenFilter(userService, secretKey), UsernamePasswordAuthenticationFilter.class)
                .addFilterBefore(new ExceptionHandlerFilter(), JwtTokenFilter.class)
                .build();
    }
}
```

<br>

```
 .addFilterBefore(new ExceptionHandlerFilter(), JwtTokenFilter.class)
```

이 부분이 추가되었다.

{: .important }
> **특정 필터에서 예외가 발생하면, 앞서 거쳐간 필터에서 예외를 처리하게 된다고 한다.**

따라서, Token을 검증하는 JwtTokenFilter 앞에, 예외처리를 하는 HandlerFilter를 추가해준다.

<br>

```java
import com.google.gson.Gson;
import io.jsonwebtoken.ExpiredJwtException;
import io.jsonwebtoken.JwtException;
import likelion.sns.Exception.ErrorCode;
import likelion.sns.Exception.ErrorDto;
import likelion.sns.Exception.SNSAppException;
import likelion.sns.domain.Response;
import org.springframework.http.MediaType;
import org.springframework.web.filter.OncePerRequestFilter;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class ExceptionHandlerFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal( HttpServletRequest request,HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        try {
            filterChain.doFilter(request, response);
        } catch (ExpiredJwtException e) {
            //토큰의 유효기간 만료
            setErrorResponse(response, ErrorCode.INVALID_TOKEN);
        } catch (JwtException | IllegalArgumentException e) {
            //유효하지 않은 토큰
            setErrorResponse(response, ErrorCode.INVALID_TOKEN);
        }
    }

    private void setErrorResponse(HttpServletResponse response, ErrorCode errorCode) {
        Gson gson = new Gson();
        response.setStatus(errorCode.getHttpStatus().value());
        response.setContentType(MediaType.APPLICATION_JSON_VALUE);
        try {
            response.getWriter().write(gson.toJson(Response.error(new ErrorDto(new SNSAppException(errorCode)))));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

위와 같이 구현하였다.

에러를 어떻게 반환하는지에 따라, `setErrorResponse` 메서드는 달라질 것이다.

`setErrorResponse` 에서 `HttpServletResponse` 에 JSON 타입으로 `write()` 파라미터로 넣어주어야 한다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221222001103442.png" alt="image-20221222001103442" style="zoom:80%;" />
</p>

> 만료된 토큰이거나, 토큰이 일치하지 않는 경우

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221222001321041.png" alt="image-20221222001321041" style="zoom:80%;" />
</p>

> 토큰을 담지 않은 경우

<br>

참고 블로그 : [https://velog.io/@hellonayeon/spring-boot-jwt-expire-exception](https://velog.io/@hellonayeon/spring-boot-jwt-expire-exception)

