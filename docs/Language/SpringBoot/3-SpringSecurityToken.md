---
layout: post
title: "· Jwt 토큰으로 특정 POST 접근 제한하기"
nav_order: 3
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/SpringSecurityToken
---

# Jwt 토큰으로 특정 POST 접근 제한하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>



지금까지, Jwts 를 이용해서 토큰을 생성하였고, 로그인시 발급하는 기능까지 구현하였다.

[회원가입 구현하기](https://inkyu-yoon.github.io/docs/Language/SpringBoot/SpringSecurityJoin)

[로그인 구현하기](https://inkyu-yoon.github.io/docs/Language/SpringBoot/SpringSecurityLogin)


**회원가입(join) 과 로그인(login) 을 제외한 모든 POST 요청은 토큰을 헤더에 담아서 요청해야 작동하게끔 할 것이다.**

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221205160330502.png" alt="image-20221205160330502" style="zoom:80%;" />
</p>


이제, 위와 같이 생성한 토큰을 Authorization 헤더에 담아서 요청을 해야 정상작동하도록 구현할 것이다.

<br>

## 회원가입(join) 과 로그인(login) 을 제외한 모든 POST 요청, 토큰이 없는 경우 접근 막기

<br>

```java
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
                .antMatchers(HttpMethod.POST,"api/v1/**").authenticated()
                .and()
                .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .addFilterBefore(new JwtTokenFilter(userService, secretKey), UsernamePasswordAuthenticationFilter.class)
                .build();
    }
}
```

이전에 만들어두었던 `SecurityFilterChain` 을 약간 수정하면 된다.

<br>

```
.antMatchers("api/v1/users/join", "/api/v1/users/login").permitAll()
.antMatchers(HttpMethod.POST,"api/v1/**").authenticated()
```

수정된 부분은 위와 같은데,

"api/v1/users/join" 와 "api/v1/users/join" 접근은 항상 허용하고

"api/v1/**" 경로로 `POST` 요청을 받을 시 접근 권한을 확인하겠다는 의미이다.

<br>

```
.antMatchers(HttpMethod.POST,"api/v1/**").authenticated()
.antMatchers("api/v1/users/join", "/api/v1/users/login").permitAll()
```

{: .warning }
> 참고로 위와 같이 구문의 순서가 바뀌면 안된다.
>
> 필터체인은 순서대로 동작하기 때문에, 위 같은 경우는 join 과 login 작업의 자유로운 접근이 차단되기 때문이다.

<br>

```
 .addFilterBefore(new JwtTokenFilter(userService, secretKey), UsernamePasswordAuthenticationFilter.class)
```

그리고 이 구문을 통해서 `UsernamePasswordAuthenticationFilter` 필터를 지나기 전에 `JwtTokenFilter` 를 거치게끔 설정하였다.

<br>

파라미터로 `secretKey` 를 사용해야하기 때문에

```
@Value("${jwt.token.secret}")
    private String secretKey;
```

환경변수로 `secretKey` 값을 받는 이 부분도 꼭 추가해주어야 한다.

<br>

## JwtTokenFilter 구현하기

<br>

먼저 완성된 코드는 아래와 같다.

```java
@Slf4j
@RequiredArgsConstructor
public class JwtTokenFilter extends OncePerRequestFilter {


    private final UserService userService;
    private final String secretKey;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {

        final String authorization = request.getHeader(HttpHeaders.AUTHORIZATION);
        if (authorization == null || !authorization.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);
            return;
        }
        String token;
        try {
            token = authorization.split(" ")[1];
        } catch (Exception e) {
            log.error("token 추출에 실패했습니다.");
            filterChain.doFilter(request, response);
            return;
        }

        if (JwtTokenFilter.isExpired(token, secretKey)) {
            filterChain.doFilter(request, response);
            return;
        }
        
        String userId = JwtTokenUtil.getUserId(extractClaims(token,secretKey));
        User user = userService.getUserByUserId(userId);

        UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(user.getUserId(), null, List.of(new SimpleGrantedAuthority(user.getUserRole().name())));

        authenticationToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));

        SecurityContextHolder.getContext().setAuthentication(authenticationToken);

        filterChain.doFilter(request, response);
    }
    
    private static Claims extractClaims(String token, String secretKey) {
        return Jwts.parser().setSigningKey(secretKey).parseClaimsJws(token).getBody();
    }
    
    public static boolean isExpired(String token, String secretKey) {
        Date expiredDate = extractClaims(token, secretKey).getExpiration();
        return expiredDate.before(new Date());
    }

   
}

```


먼저, `OncePerRequestFilter` 를 상속받아 메서드를 Override해서 필터를 구현한다.

`OncePerRequestFilter` 는 필터가 두 번 실행되는 현상을 방지할 수 있는 필터로, 인증 · 인가와 같이 한번만 거쳐도 되는 로직에서 유용하게 사용할 수 있는 필터이다.

이제, 상속받은 `doFilterInternal` 메서드를 오버라이딩 해야한다.

<br>

```java
 final String authorization = request.getHeader(HttpHeaders.AUTHORIZATION);
        if (authorization == null || !authorization.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);
            return;
        }
```

먼저, 요청받은 서블릿(`HttpServletRequest`)을 `getHeader()` 메서드를 사용해서 헤더 중 AUTHORIZATION 데이터를 가져온다.

Jwt 방식으로 토큰을 발급했고, Jwt 토큰은 `OAuth` 방식임을 알려주기 위해 `Bearer 토큰` 과 같은 문자열로 시작된다.

만약, 토큰이 없거나(null) `OAuth` 방식이 아닌 경우(Bearer로 시작하지 않는 경우) 권한을 부여하기 전에 다음 필터를 진행시킨다.

<br>

```java
  	    String token;
        try {
            token = authorization.split(" ")[1];
        } catch (Exception e) {
            log.error("token 추출에 실패했습니다.");
            filterChain.doFilter(request, response);
            return;
        }

```

`Bearer 토큰` 과 같은 문자열로 전달되기 때문에, 공백으로 split 한 뒤 1번째 index는 순수 토큰 만 추출할 수 있다.

만약, 에러가 발생하면 위와 마찬가지로, 권한을 부여하기 전에 다음 필터를 진행시킨다.

<br>

```java
    private static Claims extractClaims(String token, String secretKey) {
        return Jwts.parser().setSigningKey(secretKey).parseClaimsJws(token).getBody();
    }
    
    public static boolean isExpired(String token, String secretKey) {
        Date expiredDate = extractClaims(token, secretKey).getExpiration();
        return expiredDate.before(new Date());
    }
```

`extractClaims` 메서드는, 암호화된 토큰을 통해서 우리가 토큰 생성시 입력했던 `secretKey` 와 토큰을 이용해서 토큰 만료 일자를 추출하는 메서드이다.

`Jwts.parser().setSigningKey(secretKey).parseClaimsJws(token).getBody();` 와 같이 적절한 메서드에 secretKey 와 token을 넣어주면 된다.

그렇게 `Claims` 타입으로 반환되고 토큰 생성시 `setExpiration()` 했던 것 처럼 `getExpiration()` 하면 만료 일자를 구할 수 있다.

그리고 `expiredDate.before(new Date());` 를 통해서

만료일자가 현재 시간 전이라면 이미 유효기간이 끝났다는 뜻이므로 **True** 를,

현재 시간 이후라면 만료되기 전이므로 **False**를 반환할 것이다.

<br>

```java
       if (JwtTokenFilter.isExpired(token, secretKey)) {
            filterChain.doFilter(request, response);
            return;
        }
```

따라서, 만료되었다면 권한을 주기전에 다음 필터를 진행시킨다.

<br>

```java
        String userId = JwtTokenUtil.getUserId(extractClaims(token,secretKey));
        User user = userService.getUserByUserId(userId);
```

이제 만료되지 않은 정상적인 토큰을 가졌다면 위 로직을 거친다.

이 부분을 설명하기 전에 `JwtTokenUtil` 부분을 먼저 보면,

<br>

```java
public class JwtTokenUtil {

    public static String getUserId(Claims claims) {
        return claims.get("userId").toString();
    }
    public static String createToken(String userId, String key, long expireTimeMs) {
        Claims claims = Jwts.claims();
        claims.put("userId", userId);

        return Jwts.builder()
                .setClaims(claims)
                .setIssuedAt(new Date(System.currentTimeMillis()))//현재시간
                .setExpiration(new Date(System.currentTimeMillis() + expireTimeMs))//현재 시간 +종료 시간
                .signWith(SignatureAlgorithm.HS256, key)
                .compact();
    }
}
```

`claims.put("userId",userId)` 를 통해서 토큰을 생성할 때, `userId`의 값을 토큰에 저장했었고, 

`getUserId`는 저장해둔 `userId` 를 꺼내서 String 타입으로 변환하는 단순한 메서드 이다.

그렇게 토큰에 저장해둔 `userId`와 UserService에 구현해 둔 `userId`를 이용해서 User 객체를 가져온다.

<br>

```java
 	//UserService에 구현된 메서드
    public User getUserByUserId(String userId) {
        return userRepository.findByUserId(userId)
                .orElseThrow(() -> new HospitalReviewAppException(ErrorCode.USER_NOT_FOUNDED, ""));
    }
```

참고로 getUserByUserId 메서드는 위와 같고, 가입된 아이디가 아니라면 예외처리를 한다.

<br>

```java
        UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(user.getUserId(), null, List.of(new SimpleGrantedAuthority(user.getUserRole().name())));

        authenticationToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));

        SecurityContextHolder.getContext().setAuthentication(authenticationToken);

        filterChain.doFilter(request, response);
```

`UsernamePasswordAuthenticationToken` 객체에 `userId` 와 권한을 부여하는 클래스인 `SimpleGrantedAuthority` 에

`userId` 에 해당하는 `userRole` 을 넣어준다. (파라미터가 컬렉션 타입이므로 List.of를 사용했다.)

<br>

{: .highlight }
3번째 파라미터로 권한을 설정해주면 FilterChain에 `.antMatchers("/user").hasRole("USER")`  와 같은 구문으로 리소스 접근 제어를 할 수 있는 것 같다...?

<br>

`authenticationToken` 에 `setDetails()` 메서드로 `new WebAuthenticationDetailsSource().buildDetails(request)` 와 `SecurityContextHolder.getContext().setAuthentication(authenticationToken);` 구문으로 현재 사용자가 인증 되었음을 설정해준다.

<br>

## DTO 준비

<br>

```java
@Getter
@AllArgsConstructor
@NoArgsConstructor
public class ReviewCreateRequest {

    private String title;
    private String content;
    private String userId;
    private Long hospitalId;

}
```

POST 요청 시, 댓글 제목, 내용, 사용자 아이디, 병원 아이디가 있어야 DB에 저장할 수 있다.

<br>

```java
@Getter
@AllArgsConstructor
@NoArgsConstructor
public class ReviewCreateResponse {

    private String title;
    private String content;
    private String userId;
    private String message;

    public ReviewCreateResponse(Review review, String message) {
        this.title = review.getTitle();
        this.content = review.getContent();
        this.userId = review.getUserId();
        this.message = message;
    }
}
```

`Review` 를 생성자로 받아 사용자에게 전달할 `ReviewCreateResponse` 객체의 맴버들을 초기화시켜주었고, 

message는 ["리뷰 등록에 성공하였습니다."]  메세지를 나타나게 하기 위해 포함시켰다.

<br>

```java
@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Review {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "review_id")
    private Long id;

    @Column(nullable = false)
    private String title;
    @Column(nullable = false, length = 500)
    private String content;
    @Column(name = "user_id", nullable = false)
    private String userId;

    @ManyToOne(fetch = FetchType.LAZY, cascade = CascadeType.ALL)
    @JoinColumn(name = "hospital_id")
    private Hospital hospital;

    public Review(ReviewCreateRequest reviewCreateRequest,Hospital hospital) {
        this.title = reviewCreateRequest.getTitle();
        this.content = reviewCreateRequest.getContent();
        this.userId = reviewCreateRequest.getUserId();
        this.hospital = hospital;
    }
}

```

참고로 엔티티는 위와 같이 구성하였다.

<br>

## ReviewController

<br>

```java
@RestController
@RequiredArgsConstructor
@RequestMapping("api/v1/reviews")
@Slf4j
public class ReviewController {

    private final HospitalService hospitalService;
    private final ReviewService reviewService;

     @PostMapping
    public Response<ReviewCreateResponse> write(@RequestBody ReviewCreateRequest reviewCreateRequest, Authentication authentication) {
        Review review = new Review(reviewCreateRequest, hospitalService.getById(reviewCreateRequest.getHospitalId()).get());
        if (authentication.isAuthenticated()) {
            reviewService.createReview(review);
        }
        ReviewCreateResponse reviewCreateResponse = new ReviewCreateResponse(review, "리뷰 등록 성공");
        log.info("Controller user : {}", authentication.getName());

        return Response.success(reviewCreateResponse);
    }
}

```

반환 타입은, 이전에 만들어둔 `Response` 객체를 활용했다.

`ReviewCreateRequest`를 구성하고 있는 `title` , `content`, `userId`, `hospitalId` 가 post 요청과 함께 body부에 JSON 형태로 전달되면 

`hospitalId` 로 `Hospital` 객체를 찾고, `Review` 엔티티 생성자에 입력하여 `Review` 객체를 생성한다.

그리고 `if (authentication.isAuthenticated())` 조건문으로 인증된 경우에만 DB에 저장하도록 한다.

<br>

```java
@Service
@RequiredArgsConstructor
@Transactional(readOnly = true)
public class ReviewService {

    private final ReviewRepository reviewRepository;

    @Transactional
    public Review createReview(Review review) {
        Review savedReview = reviewRepository.save(review);
        return savedReview;
    }

}
```

인증이 되었다면, 생성한 엔티티를 `ReviewService` 의 `createReview` 메서드로 실제 DB에 저장한다.

저장한 뒤, 엔티티를 이용해서 다시 `ReviewCreateResponse` 객체를 생성하고 반환한다.

<br>

## 동작 확인

<br>

먼저 토큰 없이 리뷰를 등록해보겠다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221205173905657.png" alt="image-20221205173905657" style="zoom: 67%;" />
</p>

예상대로 리뷰 등록이 되지 않는다.

그러면 토큰을 발급받은 뒤 토큰을 Authorization 헤더에 포함시켜서 요청을 해보겠다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221205174106949.png" alt="image-20221205174106949" style="zoom:80%;" />
</p>

회원 가입을 했다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221205174140975.png" alt="image-20221205174140975" style="zoom:80%;" />
</p>

가입한 아이디와 비밀번호로 로그인을 했고, 토큰을 발급받았다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221205174237363.png" alt="image-20221205174237363" style="zoom:80%;" />
</p>

Authorization의 Type 을 OAuth 2.0 선택을 한 뒤 입력받은 토큰을 붙여넣기 한다.

다시 review POST 요청을 수행하면!

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221205174348556.png" alt="image-20221205174348556" style="zoom:67%;" />
</p>

정상적으로 리뷰가 등록되었다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221205175544488.png" alt="image-20221205175544488" style="zoom:80%;" />
</p>

DB에도 잘 입력되었다!

