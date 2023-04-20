---
layout: post
title: "· 메일 전송 기능을 EventHandler와 AOP를 적용해 개선하기"
nav_order: 20
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/EmailAop
---

# 메일 전송 기능을 EventHandler와 AOP를 적용해 개선하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>


## EventHandler를 적용하는 이유

aspectj에서 제공하는 ***@AfterReturning*** 어노테이션으로, 메소드가 정상적으로 실행되고 반환값을 반환한 이후에 실행되게끔 할 수 있다.

<br>

메일을 임시 비밀번호 발급 · 사용자 인증번호 전송 · 낙찰 성공 정보 전송 등등 여러가지 기능에 쓰이기 때문에,

Aop 클래스에서 emailService를 의존해서 여러 메서드를 호출하기보다는

**이벤트 발행 역할 하나만 맡는 것이 단일 책임 원칙에 적합하다고 생각이 들었다.**

<br>

## Custom Annotation 정의

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface SendMail {
    Class<?> classInfo() default Object.class;
}

```

***SendMail*** 이라는 어노테이션을 정의했다.

### @Target(ElementType.METHOD)

Method에 적용할 것이기 떄문에 MTHOD로 설정한다.

<br>

### @Retention(RetentionPolicy.RUNTIME)

런타임이 종료될때까지 적용되어야 하기 때문에 Retention을 RUNTIME으로 설정했다.

<br>

### Class<?> classInfo() default Object.class;

어노테이션 사용 시, 반환하는 class 정보도 같이 넘겨주어한다.

그 이유는 이벤트 리스너는 발행하는 클래스마다 다른 동작을 하기 때문에,

이벤트 리스너에게 객체를 전달함으로써 원하는 동작을 실행할 수 있기 때문이다.

<br>

## Service 메서드

```java
@Transactional
@SendMail(classInfo = UserFindPasswordResponse.class)
public UserFindPasswordResponse findPassword(UserFindPasswordRequest request) throws NoSuchAlgorithmException {
    User foundUser = userRepository.findByEmailAndPhone(request.getEmail(), request.getPhone())
            .orElseThrow(() -> new ShoeKreamException(USER_NOT_FOUND));

    String tempPassword = SecureCodeUtil.getTempPassword();

    foundUser.changePassword(encoder, tempPassword);

    return foundUser.toUserFindPasswordResponse(tempPassword);
}
```

***@SendMail(classInfo = UserFindPasswordResponse.class)*** 와 같이 반환되는 객체의 클래스 정보를 넘겨준다.

그리고 해당 클래스를 통해서,이벤트를 발생시켜 이벤트가 동작하도록 정의할 것이다.



## EventListner 정의

```java
@Component
@RequiredArgsConstructor
public class EmailEventListener {

    private final EmailCertificationService emailCertificationService;

    @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT, classes = UserFindPasswordResponse.class)
    public void handle(UserFindPasswordResponse event) throws MessagingException {

        emailCertificationService.sendEmailForFindPassword(event.getEmail(), event.getTempPassword());
    }
}
```

먼저, ***classes = UserFindPasswordResponse.class*** 옵션을 통해, ***UserFindPasswordResponse*** 클래스로 이벤트를 발행할 시 동작하도록 정의한다.

***phase = TransactionPhase.AFTER_COMMIT*** 옵션을 통해, 해당 메서드가 트랜잭션이 커밋된 후 동작하도록 설정하였다.

따라서, 핵심 로직이 성공하고 나서 이메일 전송이 될 것이다.

<br>



## EventPublisher 와 Aop 처리 클래스 정의

```java
@Aspect
@Component
public class SendMailEventAop implements ApplicationEventPublisherAware {

    private ApplicationEventPublisher eventPublisher;

    @Override
    public void setApplicationEventPublisher(ApplicationEventPublisher applicationEventPublisher) {
        this.eventPublisher = applicationEventPublisher;
    }

    @AfterReturning(value = "@annotation(sendMail)", returning = "retVal")
    public void afterReturning(SendMail sendMail,Object retVal) throws ....{
        Class<?> clazz = sendMail.classInfo();
        if (clazz.isInstance(retVal)) {
            Constructor<?> constructor = clazz.getDeclaredConstructor(retVal.getClass());
            Object event = constructor.newInstance(retVal);
            eventPublisher.publishEvent(event);
        }
    }
}
```



***EventListener***는 특정 이벤트를 발생시켜야 동작하기 때문에, 이벤트를 발생시키는 작업이 필요하다.

***@AfterReturning*** 으로 설정해서 ***@SendMail*** 어노테이션이 붙은 메서드가 정상적으로 동작을 마치고 값을 반환하면 메서드를 실행하게끔 한다.

<br>

메서드 내부에서는 sendMail 애노테이션의 classInfo() 메서드를 호출하여, 이벤트로 사용할 클래스 정보를 가져온다.

그리고 메서드에서 반환된 객체가 이 클래스의 인스턴스인지 검사한 후,

***getDeclaredConstructor()*** 메서드를 이용하여 해당 클래스의 생성자를 가져오고, ***newInstance()*** 메서드를 이용하여 이벤트 객체를 생성한다.

<br>

```java
public class UserFindPasswordResponse {

    private String email;
    private String tempPassword;

    public UserFindPasswordResponse(UserFindPasswordResponse userFindPasswordResponse) {
        this.email = userFindPasswordResponse.getEmail();
        this.tempPassword = userFindPasswordResponse.getTempPassword();
    }
}
```

참고로 이벤트에 사용되는 객체에는 위와 같이, 객체를 매개변수로 받는 생성자를 추가해주어야 ***constructor.newInstance(retVal);*** 구문이 정상적으로 실행된다.

<br>

마지막으로  ***eventPublisher.publishEvent(event);*** 메서드가 동작하면서 이벤트를 발생시킨다.

<br>

> 결과적으로 UserApiController는 emailService와 의존성을 끊어낼 수 있게 되었으며, 컨트롤러 코드 가독성도 향상할 수 있게 되어 핵심 로직에 집중할 수 있게 되었다.


