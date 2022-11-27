---
layout: post
title: "· @NotEmpty 사용을 위한 라이브러리 추가"
nav_order: 3
parent : TIP
grand_parent: 📚Learned
permalink: docs/Learned/TIP/NotEmpty
---

# @NotEmpty 사용을 위한 라이브러리 추가

<br>

`@NotEmpty` 어노테이션을 붙이면, 필수로 입력 값을 받아야함을 의미한다.

`null` 과 `""` 둘 다 허용하지 않게한다.

스프링 부트 2.3 이상 부터는, 이 어노테이션을 사용하기 위해서는 따로 의존성을 추가해주어야 사용할 수 있다.

```

implementation 'org.springframework.boot:spring-boot-starter-validation'

```

