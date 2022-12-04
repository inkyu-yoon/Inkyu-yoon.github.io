---
layout: post
title: "· Jwts를 이용한 로그인 구현"
nav_order: 2
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/SpringSecurityLogin
---

# Jwts를 이용한 로그인 구현
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


<br>

[【Spring Security를 이용한 회원가입 구현】](https://inkyu-yoon.github.io/docs/Language/SpringBoot/SpringSecurityJoin)

Security를 이용해서 회원가입 기능을 만들었으니 로그인 기능을 구현해보겠다.

<br>

## DTO 준비

<br>

```java
@Getter
@AllArgsConstructor
@NoArgsConstructor
public class UserLoginRequest {
    private String userId;
    private String password;
}
```

로그인 과정에서는 `userId` 와 `password` 만 사용자에게 받으면 된다.

```java
@Getter
@AllArgsConstructor
@NoArgsConstructor
public class UserLoginResponse {
    private String token;
}
```

그리고 응답은 토큰으로 응답할 것이다.



<br>

## Service 구현
<br>

서비스 클래스를 구현하기 전에 `application.yml` 에 아래 구문을 추가한다.

```yaml
jwt:
  token:
    secret: hello
```

토큰을 생성하기 위해서는 파라미터로 문자열 형태의 `secretKey` 를 전달해야한다.

위 처럼, 설정해둔 `hello` 를 사용하지 말고, 환경변수로 `JWT_TOKEN_SECRET = 사용자 설정 값` 을 등록해서 보안이 필요하다.

```java
@Service
@RequiredArgsConstructor
@Transactional(readOnly = true)
public class UserService {

    private final UserRepository userRepository;
    private final BCryptPasswordEncoder encoder;

    @Value("${jwt.token.secret}")
    private String secretKey;
    private long expiredTimeMs = 1000 * 60 * 60; //1시간

    public String login(String userId, String password) {
        User user = userRepository.findByUserId(userId)
                .orElseThrow(() -> new HospitalReviewAppException(ErrorCode.USER_NOT_FOUNDED, String.format("%s는 가입된 적이 없습니다.", userId)));

        if(!encoder.matches(password,user.getPassword())){
            throw new HospitalReviewAppException(ErrorCode.INVALID_PASSWORD, String.format("userName 또는 password가 잘 못 되었습니다."));
        }
        return JwtTokenUtil.createToken(userId, secretKey, expiredTimeMs);
    }

}

```



`@Value("${jwt.token.secret}")` 어노테이션을 통해서 환경변수로 등록한 secretKey가 입력된다.

`expiredTimeMs` 는 토큰 만료 시간이고, 단위는 ms 이므로 1시간 설정을 위해서 위와 같이 설정했다.



`login` 메서드를 살펴보면, `userId` 와 `password` 를 입력받는다.

먼저, userId로 DB에 해당하는 아이디가 저장되어 있는지 확인한다. `Optional` 클래스 메서드인 `orElseThrow()`로 null 값인 경우 예외를 발생시키게끔 한다.

이전에 `ErrorCode` Enum 클래스에 만들어둔  `USER_NOT_FOUNDED(HttpStatus.NOT_FOUND, "not found error")` 를 `HospitalReviewAppException` 에 전달한다.

`ExceptionManager` 는 에러 발생시 404 NOT FOUND 상태코드로 응답할 것이고  `not found error` 라는 메세지와 null 응답을 할 것이다.

그리고, `BCryptPasswordEncoder` 클래스의 `matches` 메서드를 사용해서 입력받은 패스워드와 암호화된 상태로 DB에 저장된 패스워드가 같은 패스워드인지 확인한다.

다르다면 마찬가지로 에러를 발생시킨다.

정상적으로 로그인을 마친 경우, `JwtTokenUtil.createToken(userId, secretKey, expiredTimeMs);` 을 통해서 문자열 형태의 토큰을 생성한 뒤 반환한다.

<br>

## JwtTokenUtil 구현

<br>

`JwtTokenUtil` 는 문자열 형태의 토큰을 발급하기 위해 만든 클래스이다.

인증된 사용자에게 위 토큰을 부여하고, 허가된 리소스에 접근할 수 있도록 한다.

위 기능을 위해서 `Jwts` 클래스를 사용해야한다.


```java
public class JwtTokenUtil {
    public static String createToken(String userId, String key, long expireTimeMs) {
        Claims claims = Jwts.claims();
        claims.put("userId", userId);

        return Jwts.builder()
                .setClaims(claims)
                .setIssuedAt(new Date(System.currentTimeMillis()))//현재시간
                .setExpiration(new Date(System.currentTimeMillis() + expireTimeMs))//현재시간 + 종료시간 = 만료 시간
                .signWith(SignatureAlgorithm.HS256, key) //해싱 알고리즘으로 입력받은 키를 암호화
                .compact();
    }
}
```



`Claims` 클래스를 `Jwts` 클래스의 메서드로 초기화한 뒤, Token을 구성하는데 사용할 것이다.

일종의 `Map` 컬렉션과 비슷한 역할이라고 생각하면 된다.

위 메서드를 통해서, 결과적으로 암호화된 형태의 토큰이 생성된다.

<br>

## PostMapping controller 구현

<br>

```java
@RestController
@RequestMapping("api/v1/users")
@RequiredArgsConstructor
@Slf4j
public class UserController {

    private final UserService userService;

    @PostMapping("/login")
    public Response<UserLoginResponse> login(@RequestBody UserLoginRequest userLoginRequest) {
        String token = userService.login(userLoginRequest.getUserId(), userLoginRequest.getPassword());
        return Response.success(new UserLoginResponse(token));
    }
}
```

사용자에게 id와 password를 POST의 형태로 입력받아 `UserLoginRequest` 객체를 초기화 한 뒤, 사용한다.

`UserService`에 구현한 `login` 메서드에 `id` 와 `password` 를 넘겨준다.

정상적으로, 로그인 인증이 되었으면 token 값이 반환될 것이다.

결과적으로 token 을 `UserLoginResponse` 객체에 담은 뒤, 반환한다.

<br>

## 동작 확인

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221205011825158.png" alt="image-20221205011825158" style="zoom:80%;" />
</p>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221205011836371.png" alt="image-20221205011836371" style="zoom:80%;" />
</p>

위와 같이 회원가입을 진행했고, DB에도 암호화된 상태로 데이터가 저장이 되었다.

로그인을 시도해보겠다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221205011925726.png" alt="image-20221205011925726" style="zoom:80%;" />
</p>

회원 가입할때 설정한 id와 password로 정상 응답을 확인할 수 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221205012017048.png" alt="image-20221205012017048" style="zoom:80%;" />
</p>

password를 다른 값으로 설정한 뒤, Post 했더니 실패 응답을 받을 수 있었다.