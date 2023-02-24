---
layout: post
title: "· MockedStatic을 사용해 static method 테스트 코드에 적용하기"
nav_order: 5
parent : TIP
grand_parent: 📚Learned
permalink: docs/Learned/TIP/MockedStatic
---

# MockedStatic을 사용해 static method 테스트 코드에 적용하기

<br>

Service Test를 하면서

Service 메서드 로직 중에, static Method를 사용하는 경우 Mock 테스트 코드를 짤때 에러가 계속 발생했다.

<br>

```java
    /**
     * 회원 로그인
     */

    public UserLoginResponseDto loginUser(UserLoginRequestDto loginRequest) throws SQLException {
        String requestUserName = loginRequest.getUserName();
        String requestPassword = loginRequest.getPassword();

        // 로큰 요청한 userName이 가입된 적 없으면 에러를 발생시킴
        User found = userValid(requestUserName);

        // 가입된 회원이지만, 입력한 비밀번호화 DB에 입력된 비밀번호가 다를 경우, 에러를 발생시킴
        if (!encoder.matches(requestPassword, found.getPassword())) {
            throw new SNSAppException(ErrorCode.INVALID_PASSWORD, "잘못된 비밀번호 입니다");
        }

        // 로그인에 성공할 시, token을 create 하고 반환
        return new UserLoginResponseDto(JwtTokenUtil.createToken(requestUserName, secretKey));
    }
```

로그인 로직 마지막에 `JwtTokenUtil.createToken(requestUserName, secretKey)` 이 부분이 문제였다.

<br>

에러가 발생한 테스트 코드는 아래와 같다.

```java
        @Test
        @DisplayName("로그인 성공 테스트")
        void loginSuccess() {
            given(requestDto.getUserName())
                    .willReturn("name");
            given(requestDto.getPassword())
                    .willReturn("password");
            given(userRepository.findByUserName(any()))
                    .willReturn(Optional.of(mockUser));
            given(encoder.matches(any(), any()))
                    .willReturn(true);
            given(JwtTokenUtil.createToken("username","secretkey"))
                    .willReturn("jwt");

            assertDoesNotThrow(() -> userService.loginUser(requestDto));
        }
```

위와 같이 테스트 코드를 짜고 실행해보니

```
org.mockito.exceptions.misusing.MissingMethodInvocationException: 
when() requires an argument which has to be 'a method call on a mock'.
For example:
    when(mock.getArticles()).thenReturn(articles);
```

이런 에러가 발생했다.

그래서 mock 객체로 선언을 안해서 그렇구나 하고

```
@Mock
JwtTokenUtil jwtTokenUtil;
```

선언을 해준 뒤, 다시 실행시켜보았다.

그래도 같은 에러가 발생했다.

static 메서드라, 인스턴스로는 사용이 안되고..

방법을 찾아보니,  `MockedStatic` 이라는 것을 사용해야했다.

Mockito는 final 과 static 메서드를 mocking하는 것을 지원하지 않는다고 한다.


```java
        @Test
        @DisplayName("로그인 성공 테스트")
        void loginSuccess() {
                
        MockedStatic<JwtTokenUtil> jwtTokenUtilMockedStatic = mockStatic(JwtTokenUtil.class);

        given(requestDto.getUserName())
        .willReturn("name");
        
        given(requestDto.getPassword())
        .willReturn("password");
        
        given(userRepository.findByUserName(any()))
        .willReturn(Optional.of(mockUser));
        
        given(encoder.matches(any(), any()))
        .willReturn(true);
        
        given(JwtTokenUtil.createToken(any(), eq("secret")))
        .willReturn("jwt token");

        assertDoesNotThrow(() -> userService.loginUser(requestDto));

        jwtTokenUtilMockedStatic.close();
        
        }

```

위와 같이 테스트 코드를 구성하니, 테스트 코드가 잘 돌아갔다!