---
layout: post
title: "· JPA 쿼리 파라미터 로그 남기기"
nav_order: 2
parent : TIP
grand_parent: 📚Learned
permalink: docs/Learned/TIP/SQLLog
---

# JPA 쿼리 파라미터 로그 남기기

<br>


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221115221939500.png" alt="image-20221115221939500" style="zoom:150%;" />
</p>


위와 같이 어떤 파라미터가 들어갔는지 확인할 수 없을때, 사용할 수 있는 설정이 있다.

```yaml
logging:
  level:
    org.hibernate.type: trace
```

`yml` 파일에 위와 같은 설정을 추가한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221115222310105.png" alt="image-20221115222310105" style="zoom: 150%;" />
</p>


위와 같은 로그를 남기는 것을 볼 수 있다.

외부 라이브러리를 사용하면, 조금 더 직관적으로 확인할 수 있다.

```
    implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.8.1'
```

`build.gradle` 에 위 구문을 추가한다. (gavlyu를 검색하면 라이브러리를 검색해서 입력할 수 있다.)


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221115222638762.png" alt="image-20221115222638762" style="zoom:150%;" />
</p>


위와 같이, 어떤 값이 입력되었는지 확인할 수 있다.

> 외부 라이브러리는 시스템 자원을 사용하므로, 개발 단계에서는 사용해도 되지만, 
> 
> **운영 시스템에 적용하는 경우는, 성능 테스트를 꼭 하고 사용하는 것이 좋다.**
