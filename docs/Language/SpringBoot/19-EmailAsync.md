---
layout: post
title: "· 이메일 전송 기능을 비동기 처리해서 응답속도 개선하기"
nav_order: 19
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/EmailAsync
---

# 이메일 전송 기능을 비동기 처리해서 응답속도 개선하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

현재 프로젝트에서 구현한 기능 중에 하나인, 임시 비밀번호 발급 로직은 다음과 같다.

1. 사용자가 **비밀번호를 잊어버린 경우**, 이메일과 핸드폰 번호 정보를 전달해서 **임시 비밀번호 발급을 요청**한다.
2. 서버측은 전달한 이메일 · 핸드폰 번호로 **가입된 회원이 존재하는지 확인한다.**
3. 가입된 회원이 없으면 예외처리하고 존재하면 그 **회원의 비밀번호를 임시 비밀번호로 변경한다.**
4. 회원에게도 **임시 비밀번호를 이메일로 전송해준다.**

<br>

## 개선하기로 결정한 이유

```java
    @PutMapping("/find-password")
    public ResponseEntity<Response<String>> findPassword(....).. {
        String tempPassword = userService.findPassword(request);
        emailCertificationService.sendEmailForFindPassword(request.getEmail(), tempPassword);
        return ResponseEntity.ok(Response.success("ok"));
    }
```

개선 전, 컨트롤러 메서드를 보면 메일 전송과 비밀번호 수정 기능이 한 컨트롤러에서 처리된다.

먼저, ***userService.findPassword()*** 에서 사용자 정보 확인 후, 임시 비밀번호로 변경한 뒤 임시 비밀번호를 반환한다.

그리고 ***emailCertificationService.sendEmailForFindPassword()*** 에서 사용자에게 메일을 전송한다.



하나의 HTTP 요청에서 회원 정보 변경과 이메일 전송까지 처리하기 때문에,

`JavaMailSender` 를 사용해본 사람들은 알겠지만 응답속도가 매우 느리다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230419022945448.png" alt="image-20230419022945448" style="zoom:80%;" />
</p>

로컬 환경 기준으로, 응답 소요 시간을 확인해보니, ***4.62초***가 소요되는 것을 확인할 수 있다.

💡 따라서, 사용자에게는 임시비밀번호 발급이 완료 되었다는 응답을 빠르게 보내주고, 이메일 전송 과정은 비동기로 처리하기로 결정했다.

<br>

📌 사실, 지금 개선하는 비밀번호 찾기 기능에서는 빠른 응답속도가 크게 중요한 기능은 아닐 수 있다.

하지만, 상품 입찰 취소나 낙찰 같은 기능도 추가할 예정인데, 이런 상황에서는 취소 및 낙찰 여부를 빠르게 알려주는 것이 중요하다는 생각을 했고

이메일 전송 때문에 더 중요한 취소 및 낙찰 여부 응답을 느리게 하는 것은 좋지 않다고 생각이 들었기 때문에 개선하기로 결정했다.





<br>

## 비동기 처리

### AsyncConfigurer 인터페이스 구현

<br>

```java
@Configuration
@EnableAsync
@Slf4j
public class AsyncConfiguration implements AsyncConfigurer {

    @Override
    @Bean(name = "mailExecutor")
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        // 처리할 작업의 예상되는 동시 처리 수, 설정한 수 만큼 스레드가 미리 만들어져 있다.
        executor.setCorePoolSize(2);

        // 만들어져 있는 스레드가 부족한 경우, max로 설정한 값만큼 스레드를 만들어서 작업을 처리한다.
        executor.setMaxPoolSize(5);

        // max로 설정한 스레드보다 동시 요청이 많아지면, 대기할 요청의 개수
        executor.setQueueCapacity(10);

        // 스레드 이름 지정 (MailExecutor-{스레드넘버)
        executor.setThreadNamePrefix("MailExecutor-");

        // 스레드 풀 시작
        executor.initialize();
        return executor;
    }

    // 비동기 작업 중 발생한 예외 처리 (ex. 스레드 대기 큐에 설정한 값을 초과하는 경우..등)
    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return (ex, method, params) -> {
            log.error("Exception message - " + ex.getMessage());
            log.error("Method name - " + method.getName());
        };
    }
}
```

먼저, 이메일 전송을 비동기로 처리하기 위한 ***@Configuration*** 클래스를 정의한다.

비동기 요청에 사용될 쓰레드 풀 관련 설정값을 지정하면 되는데,

최적의 값을 찾기 위해서는 여러 번 실험과 측정을 통해 찾아야 한다고 한다.

<br>

이렇게 비동기 작업 처리를 위해 설정한 쓰레드 풀 이름을 ***@Bean(name = "mailExecutor")*** 로 지정해준다.

<br>

```java
@Async(value = "mailExecutor")
public void sendEmailForFindPassword(String email,String tempPassword) ... {

	String content = String.format("임시 비밀번호 입니다. [%s]", tempPassword);
	sendMail(MAIL_TITLE_FIND_PASSWORD, email, content);
}
```

이제 비동기로 요청할 메서드에 ***@Async*** 어노테이션을 사용하면 된다.

작업을 요청할 Executor Bean 이름을  ***mailExecutor***로 지정했으므로, ***@Async(value = "mailExecutor")*** 로 명시해준다.

<br>


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230419225709935.png" alt="image-20230419225709935" style="zoom:80%;" />
</p>

요청 결과가 4.62s에서 0.725s로 응답속도가  약 6.38배 개선되었다.

