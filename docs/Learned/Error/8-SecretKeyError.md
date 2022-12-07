---
layout: post
title: "· Jwt 토큰 생성 시 에러"
nav_order: 8
parent : Error
grand_parent: 📚Learned
permalink: docs/Learned/Error/SecretKeyError
---



```
java.lang.IllegalArgumentException: secret key byte array cannot be null or empty.
	at io.jsonwebtoken.lang.Assert.notEmpty(Assert.java:204) ~[jjwt-0.9.1.jar:0.9.1]
	at io.jsonwebtoken.impl.DefaultJwtBuilder.signWith(DefaultJwtBuilder.java:88) ~[jjwt-0.9.1.jar:0.9.1]
	at io.jsonwebtoken.impl.DefaultJwtBuilder.signWith(DefaultJwtBuilder.java:100) ~[jjwt-0.9.1.jar:0.9.1]
	at practice.security.token.JwtTokenUtil.createToken(JwtTokenUtil.java:18) ~[main/:na]
	at practice.security.controller.UserController.getToken(UserController.java:41) ~[main/:na]
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:na]
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:na]
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~
```

Jwts 로 토큰 발급하는 과정에서 이러한 에러가 발생했고, `application.yml`설정도 잘 해주었고 환경변수로 key 값을 잘 넣어줬는데 왜 null이라고 하는지 이해가 안됐다.

그러다가, 서칭을 통해서 이유를 알게 되었는데, key 값으로 설정한 문자열 길이가 너무 짧으면 발생하는 에러였다.

나는 간단하게 `hi` 라는 값으로 입력해놨었는데, 2문자라서 에러가 발생했던 것 같다.


<br>

```
 public static String createToken(String userId, String key, long expiredTimeMs) {
        Claims claims = Jwts.claims();
        claims.put("userId", userId);

        return Jwts.builder()
                .setClaims(claims)
                .setIssuedAt(new Date(System.currentTimeMillis()))
                .setExpiration(new Date(System.currentTimeMillis() + expiredTimeMs))
                .signWith(SignatureAlgorithm.HS256, key)
                .compact();
    }
```



```
.signWith(SignatureAlgorithm.HS256, key)
```



위 createToken 메서드에서 `signWith` 부분에 매개변수로 key 값으로 들어가는 인자의 길이가 너무 짧은 경우 발생한다.

4~5 길이 이상의 문자로 설정해야 에러를 방지할 수 있다.