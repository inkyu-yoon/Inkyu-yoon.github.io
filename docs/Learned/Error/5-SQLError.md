---
layout: post
title: "· SQLException 에러"
nav_order: 5
parent : Error
grand_parent: 📚Learned
permalink: docs/Learned/Error/SQLError
---

# SQLException

<br>

```
java.sql.SQLException: Access denied for user 'root'@'218.155.35.113' (using password: YES)
```



gradle로 빌드한 테스트 파일을 돌리는데 위와 같은 에러가 발생했었다.

확인해보니 `application.yml` 파일이

```
server:
  servlet:
    encoding:
      force-response: true

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: ${DB_HOST}
    username: root
    password: ${DB_PASSWORD}

  jpa:
    show-sql: true
    database-platform: org.hibernate.dialect.MySQL8Dialect
    database: mysql
    hibernate.ddl-auto: update



```

위와 같이, datasource가 환경변수로 입력받게끔 구현했었고, 테스트 소스파일에서는 환경변수가 전달되지 않아서 발생한 오류였다.



따라서, 테스트 파일을 실행하는 소스 파일에서 환경변수를 추가해줬더니, 정상적으로 실행되었다. (혹은, application 파일에다 직접 입력해도 된다.)