---
layout: post
title: "· WebMvcTest 중 NoSuchBeanDefinitionException 에러"
nav_order: 6
parent : Error
grand_parent: 📚Learned
permalink: docs/Learned/Error/WebMvcError
---

# WebMvcTest 중 NoSuchBeanDefinitionException 에러 해결

<br>

```
java.lang.IllegalStateException: Failed to load ApplicationContext

	at org.springframework.test.context.cache.DefaultCacheAwareContextLoaderDelegate.loadContext(DefaultCacheAwareContextLoaderDelegate.java:98)
    ...
Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'hospitalController' defined in file [C:\Users\ikyoo\Desktop\멋쟁이사자처럼 백엔드 스쿨\병원웹사이트프로젝트\web\out\production\classes\hospital\web\controller\HospitalController.class]: Unsatisfied dependency expressed through constructor parameter 0; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'hospital.web.service.HospitalService' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {}
	at org.springframework.test.context.cache.DefaultCacheAwareContextLoaderDelegate.loadContext(DefaultCacheAwareContextLoaderDelegate.java:90)
	... 72 more
Caused by: org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'hospital.web.service.HospitalService' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {}
	at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:791)
	... 90 more
```

<br>

WebMvc 테스트 코드에서 위와 같은 에러가 계속해서 발생했다.

근데, 의아한점이 나는 UserController 라는 파일 테스트를 실행중이었는데, 자꾸, hospital 관련 소스 코드에서 에러를 발생시키는 상황이었다.

해결책은, `@WebMvcTest(테스트하기를 원하는 클래스.class)` 를 명시해주어야 에러를 발생시키지 않는다.

`@WebMvcTest(UserController.class)` 이처럼 테스트 하기를 원하는 클래스를 명시하니 에러가 발생하지 않게 되었다.
