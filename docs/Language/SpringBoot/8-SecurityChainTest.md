---
layout: post
title: "· Security Chain Filter 포함해서 Controller Test 하기"
nav_order: 8
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/SecurityChainTest
---

# Security Chain Filter 포함해서 Controller Test 하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

## Security Filter Chain 설정

나는 `PostRestController`에 게시글을 작성하는 `POST` 메서드를 구현했다.

```java
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
                .antMatchers( "/api/v1/users/{userId}/role/change").hasAnyRole("ADMIN")
                .antMatchers(HttpMethod.POST, "/api/v1/**").authenticated()
                .antMatchers(HttpMethod.PUT, "/api/v1/**").authenticated()
                .antMatchers(HttpMethod.DELETE, "/api/v1/**").authenticated()
                .and()
                .exceptionHandling()
                .authenticationEntryPoint(new CustomAuthenticationEntryPointHandler())
                .accessDeniedHandler(new CustomAccessDeniedHandler())
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



위와 같은 Security Chain 을 설정하였고,

```
  .addFilterBefore(new JwtTokenFilter(userService, secretKey), UsernamePasswordAuthenticationFilter.class)
                .addFilterBefore(new ExceptionHandlerFilter(), JwtTokenFilter.class)
```

위 구문에 의해서,

Authorization 헤더에 담긴 `Bearer <토큰>` 형식의 JWT 토큰의 유효성을 확인하게끔 구현하였었다.

구현한 뒤, 테스트 코드를 작성했다.

게시글 작성 성공 테스트 코드를 작성할 때, 단순히 `@WithMockUser` 어노테이션으로 작성했을때, 

뭔가 찝찝한 기분이 들었고 여러가지 이유 때문에, 이게 맞는 테스트인가 고민을 했다.

<br>

## Is it right?

<br>

### 1. 토큰을 헤더에 담아서 요청하지 않았는데 테스트 성공

<br>


```java
        @Test
        @DisplayName("게시글 작성 테스트")
        @WithMockUser
        void postWriteSuccess() throws Exception {
            PostWriteRequestDto postWriteRequestDto = new PostWriteRequestDto("title", "body");
            Mockito.when(postService.writePost(any(), any())).thenReturn(new PostWriteResponseDto("포스트 등록 완료", 1L));
            Mockito.when(userService.findRoleByUserName(any())).thenReturn(UserRole.ROLE_USER);

            Gson gson = new Gson();
            String content = gson.toJson(postWriteRequestDto);

            mockMvc.perform(post("/api/v1/posts")
                            .with(csrf())
                            .characterEncoding("utf-8")
                            .content(content)
                            .contentType(MediaType.APPLICATION_JSON))
                    .andDo(print())
                    .andExpect(status().isOk())
                    .andExpect(jsonPath("$.resultCode").exists())
                    .andExpect(jsonPath("$.resultCode").value("SUCCESS"))

        }
```

<br>


나는 분명, 토큰을 담아서 요청하고, 그 토큰이 유효한 경우에만 작성이 성공하도록 설정했지만

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221230224124137.png" alt="image-20221230224124137" style="zoom:80%;" />
</p>

토큰을 헤더에 담지 않았음에도, 테스트 코드는 `@WithMockUser` 덕분에 통과했다.

<br>


### 2. 토큰을 이상하게 담았는데도 테스트 성공

<br>


```java
        @Test
        @DisplayName("게시글 작성 테스트")
        @WithMockUser
        void postWriteSuccess() throws Exception {
            PostWriteRequestDto postWriteRequestDto = new PostWriteRequestDto("title", "body");
            Mockito.when(postService.writePost(any(), any())).thenReturn(new PostWriteResponseDto("포스트 등록 완료", 1L));
            Mockito.when(userService.findRoleByUserName(any())).thenReturn(UserRole.ROLE_USER);

            Gson gson = new Gson();
            String content = gson.toJson(postWriteRequestDto);

            mockMvc.perform(post("/api/v1/posts")
                            .with(csrf())
                            .header(HttpHeaders.AUTHORIZATION, "Bearer ")
                            .characterEncoding("utf-8")
                            .content(content)
                            .contentType(MediaType.APPLICATION_JSON))
                    .andDo(print())
                    .andExpect(status().isOk())
                    .andExpect(jsonPath("$.resultCode").exists())
                    .andExpect(jsonPath("$.resultCode").value("SUCCESS"))
                    .andExpect(jsonPath("$.result").exists())
                    .andExpect(jsonPath("$.result.message").value("포스트 등록 완료"))
                    .andExpect(jsonPath("$.result.postId").value(1));
        }
```

위와 같이 헤더에 토큰 내용이 전혀 담기지 않는 상태로 요청을 했다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221230224258237.png" alt="image-20221230224258237" style="zoom:80%;" />
</p>

위와 같이 요청을 했는데도, 테스트는 성공을 했다.



그래서, 이 작성 테스트가 정말 의미있는 테스트 코드인가 한참 생각이 들었다.

정상적인 토큰을 넘겨주었을때, 성공하게끔 하고 싶었고, 내가 의도한 대로 테스트 환경을 구성하기 위해서는 내가 정의한 Security Filter Chain 을 적용시켜야 했다.

여러 방법들을 찾아봤고, 적용할 수 있었다.

## 적용 방법

```java
@WebMvcTest(value = PostRestController.class)
@Import(SecurityConfig.class)
@WebAppConfiguration //Controller 및 web 환경에 사용되는 빈을 자동으로 생성하여 등록한다.
class PostRestControllerTest {

    @BeforeEach
    public void setUpMOckMvc(){
        mockMvc = MockMvcBuilders
                .webAppContextSetup(wac)
                .apply(SecurityMockMvcConfigurers.springSecurity())
                .build();
    }
}
```

<br>


 ```java
@Import(SecurityConfig.class)
 ```

위 어노테이션을 클래스 레벨에 추가를 했고

<br>

```java
    @BeforeEach
    public void setUpMOckMvc(){
        mockMvc = MockMvcBuilders
                .webAppContextSetup(wac)
                .apply(SecurityMockMvcConfigurers.springSecurity())
                .build();
    }
```

위와 같이 mockMvc에 추가 설정을 해주었다.

<br>

## 적용 후 테스트 결과

<br>

위와 같이 적용을 하고 테스트를 돌려보았다.

### 1. 토큰을 안 담은 경우 (실패)

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221230224923138.png" alt="image-20221230224923138" style="zoom: 67%;" />
</p>

### 2. 토큰을 이상하게 담은 경우 (실패)

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221230230607504.png" alt="image-20221230230607504" style="zoom: 67%;" />
</p>

### 3. 토큰을 정상적으로 담은 경우 (성공)

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221230225029000.png" alt="image-20221230225029000" style="zoom: 50%;" />
</p>


내가 의도한 대로 테스트 결과를 얻을 수 있었다.


{: .highlight } 
참고자료 : [https://stackoverflow.com/questions/44467133/spring-boot-authentication-for-integration-tests/44621066#44621066](https://stackoverflow.com/questions/44467133/spring-boot-authentication-for-integration-tests/44621066#44621066)