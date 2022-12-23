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
public class SecurityConfig  {
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
                .antMatchers("/api/v1/users/join", "/api/v1/users/login").permitAll()
                .antMatchers(HttpMethod.POST, "/api/v1/**").authenticated()
                .and()
                .exceptionHandling()
                .authenticationEntryPoint(new CustomAuthenticationEntryPointHandler())
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
.exceptionHandling()
                .authenticationEntryPoint(new CustomAuthenticationEntryPointHandler())
                .and()
```

이 부분과

```
 .addFilterBefore(new ExceptionHandlerFilter(), JwtTokenFilter.class)
```

이 부분이 추가되었다.

{: .important }
> **특정 필터에서 예외가 발생하면, 앞서 거쳐간 필터에서 예외를 처리하게 된다고 한다.**

따라서, Token을 검증하는 JwtTokenFilter 앞에, 예외처리를 하는 HandlerFilter를 추가해준다.

<br>

그리고 `exceptionHandling()` 메서드를 활용해서 `.antMatchers(HttpMethod.POST, "/api/v1/**").authenticated()`

에서 발생하는 예외를 `new CustomAuthenticationEntryPointHandler()` 라는 새로운 Handler를 정의해서 처리했다.

**토큰을 아예 담지 않고 `authenticated()` 설정되어있는 요청에 들어가면 필터까지 가기전에 에러가 발생하기 때문이다.**

<br>

```java
import com.google.gson.Gson;
import likelion.sns.Exception.ErrorCode;
import likelion.sns.Exception.ErrorDto;
import likelion.sns.Exception.SNSAppException;
import likelion.sns.domain.Response;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpHeaders;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.web.AuthenticationEntryPoint;
import org.springframework.stereotype.Component;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
@Slf4j
public class CustomAuthenticationEntryPointHandler implements AuthenticationEntryPoint {


    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {
        final String authorization = request.getHeader(HttpHeaders.AUTHORIZATION);

        if (authorization == null ) {
            log.error("토큰이 존재하지 않습니다.");
            setResponse(response, ErrorCode.TOKEN_NOT_FOUND);
        }
    }
    
    private void setResponse(HttpServletResponse response, ErrorCode errorCode) throws IOException {

        Gson gson = new Gson();
        response.setStatus(errorCode.getHttpStatus().value());
        response.setContentType("application/json;charset=UTF-8");
        try {
            response.getWriter().write(gson.toJson(Response.error(new ErrorDto(new SNSAppException(errorCode)))));
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}
```

`AuthenticationEntryPoint` 를 상속받아 구현체를 오버라이딩하면 된다.

토큰 정보가 담겨있는 `authorization` 이 null 인 경우, 토큰을 발견할 수 없다는 예외 처리를 해주었다.

```java
import com.google.gson.Gson;
import io.jsonwebtoken.ExpiredJwtException;
import io.jsonwebtoken.JwtException;
import likelion.sns.Exception.ErrorCode;
import likelion.sns.Exception.ErrorDto;
import likelion.sns.Exception.SNSAppException;
import likelion.sns.domain.Response;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.MediaType;
import org.springframework.web.filter.OncePerRequestFilter;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.NoSuchElementException;

@Slf4j
public class ExceptionHandlerFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain
    ) throws ServletException, IOException {

        try {
            filterChain.doFilter(request, response);
        } catch (ExpiredJwtException e) {
            //토큰의 유효기간 만료
            log.error("만료된 토큰입니다");
            setErrorResponse(response, ErrorCode.EXPIRED_TOKEN);
        } catch (JwtException | IllegalArgumentException e) {
            //유효하지 않은 토큰
            log.error("유효하지 않은 토큰이 입력되었습니다.");
            setErrorResponse(response, ErrorCode.INVALID_TOKEN);
        } catch (NoSuchElementException e){
            //사용자 찾을 수 없음
            log.error("사용자를 찾을 수 없습니다.");
            setErrorResponse(response, ErrorCode.USERNAME_NOT_FOUND);
        }
    }

    private void setErrorResponse(HttpServletResponse response, ErrorCode errorCode) {
        Gson gson = new Gson();
        response.setStatus(errorCode.getHttpStatus().value());
        response.setContentType("application/json;charset=UTF-8");
        try {
            response.getWriter().write(gson.toJson(Response.error(new ErrorDto(new SNSAppException(errorCode)))));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

그리고 필터 예외 핸들러는 위와 같이 구현하였다.

에러를 어떻게 반환하는지에 따라, `setErrorResponse` 메서드는 달라질 것이다.

`setErrorResponse` 에서 `HttpServletResponse` 에 JSON 타입으로 `write()` 파라미터로 넣어주어야 한다.

<br>

참고 블로그

- [https://velog.io/@hellonayeon/spring-boot-jwt-expire-exception](https://velog.io/@hellonayeon/spring-boot-jwt-expire-exception)
- [https://velog.io/@dltkdgns3435/%EC%8A%A4%ED%94%84%EB%A7%81%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0-JWT-%EC%98%88%EC%99%B8%EC%B2%98%EB%A6%AC](https://velog.io/@dltkdgns3435/%EC%8A%A4%ED%94%84%EB%A7%81%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0-JWT-%EC%98%88%EC%99%B8%EC%B2%98%EB%A6%AC)
