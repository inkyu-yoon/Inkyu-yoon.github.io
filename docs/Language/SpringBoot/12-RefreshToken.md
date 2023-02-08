---
layout: post
title: "· Redis를 활용해서 Refresh Token 구현하기"
nav_order: 12
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/RefreshToken
---

# Redis를 활용해서 Refresh Token 구현하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

{: .highlight }
refresh 토큰에 대한 개념설명은 많은데, 구현 방법에 대해서는 참고자료가 많이 없어, 내가 이해한 원리를 바탕으로 구현해보았다.

<br>

## 리프레시 토큰이란?

<br>

Access Token의 유효기간을 짧게하여 보안도 높이고, 편의성도 챙기는 방법이다.

로그인을 완료하면, **유효기간이 짧은 Access Token**과 **유효기간이 긴 Refresh Token**을 발급해준다.

Access Token은 기존에 사용하던 JWT 토큰이라고 생각하면 되고, Refresh Token은 Access Token이 만료되었을 때, 새로 발급해주는 토큰이라고 생각하면 된다.

<br>

## 프로세스

<br>

1. 로그인을 하면 Access Token 과 Refresh Token을 발급해준다. ( Access 토큰만 클라이언트 쿠키에 저장 & Access 토큰과 Refresh 토큰을 레디스에 저장)
2. 클라이언트는 API를 호출할 때마다 발급받은 Access Token을 활용하여 요청을 한다.
3. 토큰을 사용하던 중, 만료되어 Invalid Token Error가 발생한다면 사용자가 보낸 Access Token으로 레디스의 Refresh Token을 찾아보고 Refresh 토큰이 유효하다면, Access Token을 다시 발급해준다.
4. Redis에 Refresh Token과 짝을 이루는 Access Token을 새로 발급한 토큰으로 업데이트한다.
5. 만약, Refresh Token도 만료되었다면, 다시 로그인을 하도록 요청한다.
6. 만약 사용자가 로그아웃을 하면, refresh token을 삭제하고 사용이 불가하도록 한다.

<br>

## 리프레시 토큰이 탈취 된다면?

<br>

서버 측에서 검증 로직이 필요하다고 한다.

<br>

1. 데이터베이스에 각 사용자에 1대1로 매핑되는 Access Token, Refresh Token 쌍을 저장한다.
2. 만약 공격자가 Refresh Token을 탈취하여, 피해자가 발급받았던 기존의 Access Token이 만료되기 전에 Access Token을 발급받아 API 요청을 한다.
3. 서버는 데이터베이스에 저장된, 아직 만료되지 않은 Access Token과 공격자가 제공한 Access Token이 다른 것을 확인한다.
4. 이러한 경우, Access Token 과 Refresh Token 둘 다 만료(폐기)시킨다.

<br>

만약 공격자가 Access Token, Refresh Token 어떠한 수단으로 둘 다 탈취한다면? 방법이 없다..ㅎ

근데, 쿠키에 Access Token을 HttpOnly 옵션으로 저장한다면, 탈취라는 개념이 적용되는게 맞나? 싶다.


<br>

## Redis 사용 vs Memcached

<br>

리프레시 토큰 개념을 활용하려면, 불가피하게 서버측에서 토큰 정보를 저장할 수 있는 곳이 필요하다.

사용하던 Mysql에 저장하기에는, 중요한 데이터가 아니라고 판단했고, 조회시 쿼리문을 실행시켜야하고, 주기적으로 데이터가 무효해지는 하는 점을 감안하여 레디스를 사용하기로 했다.

<br>

레디스는 key-value 쌍으로 데이터를 관리할 수 있는 데이터 스토리지이다. 모든 데이터를 메모리에(메인 메모리인 RAM) 저장하고 조회하는 in-memory 데이터 베이스 이다.

인메모리 상태에서 데이터를 처리하기 때문에, 다른 DB들보다 빠르고 가볍다는 장점이 있다.

Memcached 라는 인메모리 데이터 스토리지도 있지만, 성능차이가 크게 없고, Memcached는 문자열만 지원하기 때문에 Redis를 선택했다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230207014222316.png" alt="image-20230207014222316" style="zoom:67%;" />
</p>




## 구현해보기

<br>

```groovy
	implementation 'org.springframework.boot:spring-boot-starter-data-redis'
```

> redis dependency 추가

<br>

```yml
spring:
  redis:
    host: localhost
    port: 6379
```

> application.yml 추가, 배포서버의 경우 localhost가 아닌 ec2 주소를 사용

<br>

```java
@AllArgsConstructor
@Getter
@RedisHash(value = "jwtToken", timeToLive = 60*60*24*3)
public class RefreshToken {

    @Id
    private String id;

    private String refreshToken;

    @Indexed
    private String accessToken;
}
```

> redis에 저장할 객체를 정의한다.
>
> `@Indexed` 어노테이션이 있어야, 해당 필드 값으로 데이터를 찾아올 수 있다.
>
> 만료된 access Token으로 refresh Token을 찾아와서 유효성을 검사할 생각이기 때문이다.
>
> 레디스 데이터의 유효시간은, `timetoLive` 옵션으로 refresh Token과 같은 시간인 3일로 지정하였다.

 <br>

```java
@Repository
public interface RefreshTokenRepository extends CrudRepository<RefreshToken,String> {
    Optional<RefreshToken> findByAccessToken(String accessToken); 
}
```

> 이전 단계에서, `@Indexed` 어노테이션을 사용했기 때문에, `findByAccessToken()` 메서드를 정의하고 사용할 수 있다.

<br>

```java
@Service
@RequiredArgsConstructor
public class RefreshTokenService {
    private final RefreshTokenRepository refreshTokenRepository;

    @Transactional
    public void saveTokenInfo(Long employeeId, String refreshToken, String accessToken) {
        refreshTokenRepository.save(new RefreshToken(String.valueOf(employeeId), refreshToken, accessToken));
    }

    @Transactional
    public void removeRefreshToken(String accessToken) {
        refreshTokenRepository.findByAccessToken(accessToken)
                .ifPresent(refreshToken -> refreshTokenRepository.delete(refreshToken));
    }
}

```

> controller 단에서 사용하기 위해 만든 service 클래스

<br>

```java
@Configuration
public class RedisConfig {

    @Value("${spring.redis.host}")
    private String host;
    @Value("${spring.redis.port}")
    private int port;

    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        return new LettuceConnectionFactory(host, port);
    }
}
```

> `RedisConfig` 파일 정의

<br>

```java
    public static String createRefreshToken(String key) {
        Claims claims = Jwts
                .claims(); 

        return Jwts
                .builder()
                .setClaims(claims) 
                .setIssuedAt(new Date(System.currentTimeMillis())) 
                .setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60 * 24 * 3))//유효시간 (3일)
                .signWith(SignatureAlgorithm.HS256, key) //HS256알고리즘으로 key를 암호화 해줄것이다.
                .compact(); 
    }
```

> refresh 토큰에는 사용자 정보를 담을 필요가 없다.

<br>

```java
  		//access 토큰은, 사용자 정보로 생성
        String accessToken = JwtTokenUtil.createToken(requestAccount, requestEmployee.getEmail(), secretKey);
        String refreshToken = JwtTokenUtil.createRefreshToken(secretKey);

        //레디스에 저장 Refresh 토큰을 저장한다. (사용자 기본키 Id, refresh 토큰, access 토큰 저장)
        refreshTokenRepository.save(new RefreshToken(String.valueOf(requestEmployee.getId()), refreshToken, accessToken));
```

> 로그인 로직에 위와 같은 구문 추가
>
> access Token은 30분, refresh Token은 3일로 설정했다.
>
> 그리고 redis에 refresh Token 관련 정보를 저장한다.

<br>

```java
       Employee found;

        // Access Token 유효성 검증
        try {
            JwtTokenUtil.isExpired(token, secretKey);

            found = employeeRepository.findByEmail(JwtTokenUtil.getEmail(token, secretKey))
                    .orElseThrow(() -> new AppException(ErrorCode.EMPLOYEE_NOT_FOUND));

            //Access Token이 유효하지 않는다면 아래 로직을 지나갈 것
        } catch (ExpiredJwtException e) {
            log.error("💡 Access Token 이 만료되었습니다.");

            // redis에 저장되어있는 토큰 정보를 만료된 access token으로 찾아온다.
            RefreshToken foundTokenInfo = refreshTokenRepository.findByAccessToken(token)
                    .orElseThrow(() -> new AppException(ErrorCode.TOKEN_NOT_FOUND));

            String refreshToken = foundTokenInfo.getRefreshToken();

            // 만약 refresh 토큰도 만료되었다면, ExceptionHandlerFilter에서 처리된다.
            JwtTokenUtil.isExpired(refreshToken, secretKey);

            // refresh 토큰이 아직 유효하다면, redis에 함께 저장해둔, employeeId를 가져온다.
            Long employeeId = Long.valueOf(foundTokenInfo.getId());

             found = employeeRepository.findById(employeeId)
                    .orElseThrow(() -> new AppException(ErrorCode.EMPLOYEE_NOT_FOUND));

             //위 사용자 정보로 다시 Access Token을 만들어 발급한다.
            token = JwtTokenUtil.createToken(found.getAccount(), found.getEmail(), secretKey);

            //새로 발급한 Access Token으로 Redis도 업데이트를 해준다.
            refreshTokenRepository.save(new RefreshToken(String.valueOf(employeeId), refreshToken, token));
            //클라이언트 측 쿠키의 Access Token도 업데이트를 해준다.
            CookieGenerator cookieGenerator = new CookieGenerator();
            cookieGenerator.setCookieName("token");
            cookieGenerator.setCookieHttpOnly(true);
            cookieGenerator.addCookie(response, token);
            cookieGenerator.setCookieMaxAge(60 * 60);//1시간
        }
```

> JwtTokenFilter에 위 구문을 추가해준다.
>
> 쿠키로부터 받은 Access Token이 만료되었다면, 해당 Access Token과 짝을 이루는 Refresh Token이 유효한지 검사하고
>
> Refresh Token이 유효하다면, 새로운 Access Token을 발급해주는 로직이다.



일단, 위와 같이 구현했는데, 잘 구현한게 맞는지 헷갈린다.

access Token을 쿠키에 httpOnly 속성으로 발급해주기 때문에, 공격자들이 access token의 값을 알 수는 없을 것이다.

따라서, access token을 갖고 있는 사용자로 하여금 특정 행동을 유도하지 않는 이상 refresh Token을 조회할 수 없을 것이라고 생각한다.