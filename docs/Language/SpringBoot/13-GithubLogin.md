---
layout: post
title: "· Spring Boot Github 소셜 로그인 구현하기 (RestTemplate · WebClient)"
nav_order: 13
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/GithubLogin
---

# Spring Boot Github 소셜 로그인 구현하기 (RestTemplate · WebClient)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


<br>

팀 프로젝트때 구글, 네이버 OAuth 로그인 기능을 구현한 경험이 있어서 Github 로그인도 쉽게 구현할 줄 알았는데 꽤나 고전했었다.

내가 한 방법이 확실하게 맞는지는 모르겠지만, Github 계정 인증 후, DB에 저장하는 과정을 성공했기에 기록해본다.

<br>



## Github OAuth 인증 흐름과 사전 준비

<br>



간략하게 과정을 설명하면 다음과 같다.

```
1. https://github.com/login/oauth/authorize?client_id={발급받은 client_ID} 주소를 사용자에게 띄워준다.
2. 사용자는 깃허브 로그인을 통해서 인증을 한다.
3. 인증을 성공할 시, 깃허브는 {우리가 설정한 콜백 URL}?code={인증코드} 로 code값을 쿼리 파라미터 형태로 보내준다.
4. 받은 code를 이용해서 https://github.com/login/oauth/access_token 로 {client_ID,client_Secret,code}를 POST요청으로 전송한다.
5. 깃허브는 access_token을 응답해서 서버측으로 보내준다.
6. 서버는 access_token을 https://api.github.com/user 로 담아서 GET 요청을 보낸다.
7. 깃허브는 로그인한 사용자의 정보를 서버측으로 보내준다.
8. 받은 사용자 정보를 사용한다.(회원가입 혹은 로그인)
```



로직을 구현하기에 앞서,

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230325171128781.png" alt="image-20230325171128781" style="zoom:80%;" />
</p>

`Settings` > `Developer settings` > `New GitHub App` 으로 OAuth 인증 기능 구현을 위한 `Client Id` 와 `Client Secret` 을 발급받아야 한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230325171139842.png" alt="image-20230325171139842"  />
</p>

Homepage URL은 일단 로컬 환경에서 구현해볼것이기 때문에 `localhost:8080` 으로 해두었다.

Callback URL 은 어떤 사용자가 깃허브 로그인을 성공하면 깃허브 측에서 `Code` 를 쿼리 파라미터로 보내주는데, 그 파라미터를 받을 주소이다.

나는 `http://localhost:8080/oauth2/redirect` 로 설정하였다.

`http://localhost:8080/oauth2/redirect?code={코드~~}` 이런식으로 리다이렉트 될 것이다.

정상적으로 등록을 마치면 `Client ID`와 `Client Secret`정보를 얻을 수 있다.

`Client Secret`은 민감정보 이므로 노출되지 않도록 주의하자.


<br>

### application.yml 설정

<br>

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/growith-db
    username: root
    password: 12341234
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
  security:
    oauth2:
      client:
        registration:
          github:
            client-id:
            client-secret:
server:
  servlet:
    encoding:
      force-response: true
```

application.yml 설정은 위와 같다.

받아온 정보를 MySQL db에 저장하게끔 구현하였는데, 서비스 로직 설명은 생략하겠다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230325172614710.png" alt="image-20230325172614710" style="zoom: 67%;" />
</p>

client-id와 client-secret는 인텔리제이 환경변수로 넣어주었다.

<br>

### html 파일

<br>

```html
<a class="circle github" href="https://github.com/login/oauth/authorize?client_id=Iv1.1b5b1f1e08adb92e">
                                        <i class="fa fa-github fa-fw"></i>
</a>
```

html 코드는 어떤 방식이든 상관없다.

나는 일단 버튼 클릭 시 `https://github.com/login/oauth/authorize?client_id=Iv1.1b5b1f1e08adb92e` 로 이동하게끔 하는 코드를 사용하였다.

`https://github.com/login/oauth/authorize?client_id={각자 발급받은 client id}` 를 입력하면 된다.

<br>

## RestTemplate 방식

<br>

```java
@Controller
@Slf4j
@RequiredArgsConstructor
public class UserLoginController {

    @Value("${spring.security.oauth2.client.registration.github.client-id}")
    private String clientId;
    @Value("${spring.security.oauth2.client.registration.github.client-secret}")
    private String clientSecret;

    private final UserService userService;

    /**
     * 깃허브 로그인 인증 시 redirect 되는 것 GET
     * @param code (깃허브가 보내주는 code, 이 code로 accessToken을 요청해야함)
     * @return
     */
    @GetMapping("/oauth2/redirect")
    public String githubLogin(@RequestParam String code) {
        RestTemplate restTemplate = new RestTemplate();

        ResponseEntity<OAuthInfo> response = restTemplate.exchange("https://github.com/login/oauth/access_token",
                HttpMethod.POST,
                getAccessToken(code),
                OAuthInfo.class);
        String accessToken = response.getBody().getAccessToken();
        return "redirect:/githubLogin/success?access_token="+accessToken;
    }

    private HttpEntity<MultiValueMap<String,String>> getAccessToken(String code) {
        MultiValueMap<String, String> params = new LinkedMultiValueMap<>();
        params.add("client_id",clientId);
        params.add("client_secret",clientSecret);
        params.add("code",code);

        HttpHeaders headers = new HttpHeaders();
        return new HttpEntity<>(params,headers);
    }

    /**
     * 로그인 인증 후 받은 code로 post 요청을 보내면 access_token을 응답받는다. 이를 GET 하기 위한 Controller
     * @param access_token
     * @return
     */
    @GetMapping("/githubLogin/success")
    public String githubLoginSuccess(@RequestParam String access_token) {
        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<GithubProfile> response = restTemplate.exchange("https://api.github.com/user"
                , HttpMethod.GET
                , getUserInfo(access_token)
                , GithubProfile.class);

        GithubProfile userInfo = response.getBody();

        userService.login(userInfo);
        return "redirect:/";
    }
    private HttpEntity<MultiValueMap<String,String>> getUserInfo(String access_token) {
        HttpHeaders requestHeaders = new HttpHeaders();
        requestHeaders.add("Authorization", "token " + access_token);
        return new HttpEntity<>(requestHeaders);
    }
}

```

전체 코드는 위와 같다.

나눠서 설명해보겠다.

```java
@GetMapping("/oauth2/redirect")
    public String githubLogin(@RequestParam String code) {
        RestTemplate restTemplate = new RestTemplate();

        ResponseEntity<OAuthInfo> response = restTemplate.exchange("https://github.com/login/oauth/access_token",
                HttpMethod.POST,
                getAccessToken(code),
                OAuthInfo.class);
        String accessToken = response.getBody().getAccessToken();
        return "redirect:/githubLogin/success?access_token="+accessToken;
    }

    private HttpEntity<MultiValueMap<String,String>> getAccessToken(String code) {
        MultiValueMap<String, String> params = new LinkedMultiValueMap<>();
        params.add("client_id",clientId);
        params.add("client_secret",clientSecret);
        params.add("code",code);

        HttpHeaders headers = new HttpHeaders();
        return new HttpEntity<>(params,headers);
    }
```

먼저 이 부분 로직은

사용자가 로그인에 성공하면, 우리가 Github 클라이언트 id 발급받을 때, 콜백 URL로 `http://localhost:8080/oauth2/redirect` 를 설정했었다. (사람마다 다를 것)

그러면 깃허브 측은 사용자가 깃허브 로그인 성공 시, `http://localhost:8080/oauth2/redirect?code={코드~~}` 를 보내주기때문에

이를 GET 매핑하여 쿼리 파라미터인 `code`를 캐치하기 위해 설정한 메서드이다.

<br>

그리고 발급받은 `code`와 `client_id`,`client_secret`을 담아 `https://github.com/login/oauth/access_token` URL로 POST 요청을 보내야한다.

Rest 요청을 보내기 위해 `RestTemplate`를 사용하였다.

`HttpEntity` 와 `MultiValueMap` 을 활용하여 POST 요청시 함께 보낼 데이터를 추가해준다.

응답을 바로 `OauthInfo.class` 로 매핑해서 받도록 하였다.

```java
@Getter
@Setter
public class OAuthInfo {
    @JsonProperty("access_token")
    private String accessToken;
}
```

깃허브가 보내주는 `access_token`을 매핑해서 받기 위한 간단한 객체이다.

그렇게 `access_token`을 받으면 `access_token`을 쿼리 파라미터에 담아 `/githubLogin/success`로 리다이렉트 해버린다.

```java
/**
     * 로그인 인증 후 받은 code로 post 요청을 보내면 access_token을 응답받는다. 이를 GET 하기 위한 Controller
     * @param access_token
     * @return
     */
    @GetMapping("/githubLogin/success")
    public String githubLoginSuccess(@RequestParam String access_token) {
        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<GithubProfile> response = restTemplate.exchange("https://api.github.com/user"
                , HttpMethod.GET
                , getUserInfo(access_token)
                , GithubProfile.class);

        GithubProfile userInfo = response.getBody();

        userService.login(userInfo);
        return "redirect:/";
    }
    private HttpEntity<MultiValueMap<String,String>> getUserInfo(String access_token) {
        HttpHeaders requestHeaders = new HttpHeaders();
        requestHeaders.add("Authorization", "token " + access_token);
        return new HttpEntity<>(requestHeaders);
    }
```

리다이렉트 된걸 위 코드가 GET 매핑한다.

전달 받은 access_token을 `Authorization` 헤더에 담아 `https://api.github.com/user` 로 GET 요청을 하는 메서드이다.



```java
@Setter
@Getter
public class GithubProfile {
    private String email;
    private String name;
    @JsonProperty("avatar_url")
    private String imageUrl;
    private String blog;
}

```

access_token이 잘 전달되면 인증한 사용자의 다양한 정보를 받을 수 있는데, 나는 필요한 정보 몇개만 받도록 하였다.

이제 이 객체를 `userService.login`으로 전달해서 원하는 로직을 실행하도록 하면 된다!



## WebClient 방식

<br>

`RestTemplate` 방식으로 구현을 다해놓고 찾아보니 `RestTemplate` 보다 `WebClient` 를 사용하는 것을 권장한다는 말을 봤다.

```
NOTE: As of 5.0 this class is in maintenance mode, with only minor requests for changes and bugs to be accepted going forward. Please, consider using the org.springframework.web.reactive.client.WebClient which has a more modern API and supports sync, async, and streaming scenarios.
```

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230326192217582.png" alt="image-20230326192217582" style="zoom:80%;" />
</p>

RestTemplate에  Note 에도 위와 같은 내용이 써있었다.

그래서 WebClient 방식으로 변경했는데, WebClient 방식도 정리해보겠다.

```java
@Controller
@Slf4j
@RequiredArgsConstructor
public class UserLoginController {

    private final WebClientService webClientService;
    private final UserJoinService userJoinService;


    @GetMapping("/oauth2/redirect")
    public String githubLogin(@RequestParam String code) {
        String accessToken = webClientService.getAccessToken(code,"https://github.com/login/oauth/access_token");
        return "redirect:/githubLogin/success?access_token="+accessToken;
    }

    @GetMapping("/githubLogin/success")
    public String githubLoginSuccess(HttpServletResponse response, @RequestParam(name = "access_token") String accessToken) {
        UserProfile userInfo = webClientService.getUserInfo(accessToken,"https://api.github.com/user");

        String jwt = userJoinService.login(userInfo);

        CookieUtil.setCookie(response, JWT_COOKIE_NAME,jwt,COOKIE_AGE);

        return "redirect:/";
    }
}
```

먼저 Controller 로직이 좀 더 깔끔해진 것 같다.

Webclient 를 사용하는 로직은 Service 클래스를 하나 더 만들어서 분리시켰다.

그리고 테스트 코드 작성 시, 편의성을 위해 url을 파라미터로 입력받게끔 구현하였다.

방식은 동일하다. 사용자가 로그인 인증을 한 뒤에 받은 `code` 로 `access_token`을 입력 받은 뒤, 그 `access_token`을 사용해서 사용자 정보를 GET 하는 과정이다.

```java
@Configuration
public class WebClientConfig {

    @Bean
    public WebClient webClient() {
        return WebClient.builder().build();
    }
}

```

위와 같은 **Config** 클래스를 정의해서 **WebClient**가 빈으로서 등록되어 DI 주입될 수 있도록 한다.

<br>

```java

@Service
@RequiredArgsConstructor
public class WebClientService {
    @Value("${spring.security.oauth2.client.registration.github.client-id}")
    private String clientId;
    @Value("${spring.security.oauth2.client.registration.github.client-secret}")
    private String clientSecret;

    private final WebClient webClient;

    public String getAccessToken(String code,String uri) {

        AccessTokenRequest requestBody = AccessTokenRequest.builder()
                .clientId(clientId)
                .clientSecret(clientSecret)
                .code(code)
                .build();

        String response = webClient.post()
                .uri(uri)
                .header(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
                .bodyValue(requestBody)
                .retrieve()
                .toEntity(String.class)
                .block().getBody();


        return TextParsingUtil.parsingFormData(response).get("access_token");
    }
    public UserProfile getUserInfo(String accessToken,String uri) {

        return webClient.get()
                .uri(uri)
                .header("Authorization", "token " + accessToken)
                .retrieve()
                .toEntity(UserProfile.class)
                .block().getBody();

    }
}

```

**WebClient** 객체는 DI 받게될 것이다.

getAccessToken은 WebClient를 이용해서 `code`를 request body 에 담아 요청하도록 했다.

응답 받은 값을 바로 매핑하고 싶었는데 데이터가 form 형식으로 넘어와서 `TextParsingUtil` 이라는 클래스를 만들어서 String으로 받은 뒤 parsing 하였다.

```
access_token={값}&expires_in={값}&refresh_token={값}&refresh_token_expires_in={값}&scope=&token_type={값}
```

`code`를 담아서 요청하면 데이터가 위와 같이 넘어온다.

json 형식으로 오는게 아니라서 바로 매핑이 안됐다..ㅎ

```java
public class TextParsingUtil {

    public static Map<String, String> parsingFormData(String formData) {
        Map<String, String> map = new HashMap<>();
        String[] splited = formData.split("&");
        for (String s : splited) {
            String[] data = s.split("=");
            if (data.length >= 2) {
                map.put(data[0], data[1]);
            }
        }
        return map;
    }
}

```

Parsing 하는 메서드는 위와 같이 구성하였다.

먼저 `&` 단위로 쪼갠 뒤에 `=`로 split 해서 key value 형식으로 저장하였다.

중간에 `scope`는 값이 존재하지 않아서 parsing 과정에서 오류가 생길 수 있으므로

`=`로 split 했을 때, 길이가 2이상인 경우에만 저장하도록 했다.

그렇게 accessToken을 추출해서 WebclientService의 getUserInfo메서드로 다시 GET 요청을 보낸 뒤, 바로 `UserProfile`로 매핑되도록 구현하였다!



{: .highlight-title }
> 참고한 블로그
>
> 1. [https://velog.io/@bongf/study-OAuth-GitHubLogin2](https://velog.io/@bongf/study-OAuth-GitHubLogin2)











