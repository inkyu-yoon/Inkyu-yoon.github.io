---
layout: post
title: "· Spring Log4jdbc로 쿼리 실행 시간 측정하기"
nav_order: 12
parent : JPA
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/JPA/SqlTiming
---
# Spring Log4jdbc로 쿼리 실행 시간 측정하기

<br>

최근에 querydsl을 사용해서 쿼리문 갯수를 줄였었는데, 단순히 쿼리수 비교를 하는게 아니라 실행시간을 측정한 뒤 비교해보고 싶어서 방법을 찾아봤었다.

그냥 application.yml에 설정만 추가하면 되는 단순한 작업인 줄 알았는데 아니었다.ㅎㅎ

<br>

```groovy
   implementation group: 'org.bgee.log4jdbc-log4j2', name: 'log4jdbc-log4j2-jdbc4.1', version: '1.16'
```

먼저 위 dependency를 `build.gradle`에 추가한다.

<br>

그리고 resources 폴더에 `log4jdbc.log4j2.properties` 파일을 추가한다.

```
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
log4jdbc.dump.sql.maxlinelength=0
```

위 내용을 추가한다.

<br>

```yml
spring:
  datasource:
    driver-class-name: net.sf.log4jdbc.sql.jdbcapi.DriverSpy
    url: jdbc:log4jdbc:mysql://{url:포트번호}/{스키마 이름}?useSSL=false&useUnicode=true&serverTimezone=Asia/Seoul
    username: 
    password: 
  jpa:
    hibernate:
      ddl-auto: update
  mvc:
    pathmatch:
      matching-strategy: ant_path_matcher
logging:
  level:
    jdbc:
      sqlonly: off
      sqltiming: info
      resultsettable: off
      audit: off
      resultset: off
      connection: off


```

위와 같이 구성하면 된다.

`spring.datasource.driver-class-name` 에 `net.sf.log4jdbc.sql.jdbcapi.DriverSpy` 를 입력하고
`spring.datasource.url` 에는 jdbc:`log4jdbc`:mysql 과 같이 추가를 해주어야 한다.

또한 `logging.level.jdbc` 부분에서 설정을 해줄 수 있는데

| 설정                | 설명                                                     |
| ------------------- | -------------------------------------------------------- |
| jdbc.sqlonly        | SQL문을 보여줌                                           |
| jdbc.sqltiming      | SQL문과 SQL문 수행하는 시간을 같이 보여줌                |
| jdbc.resultsettable | SQL 의 결과로 조회된 데이터를 table 형태로 로그를 보여줌 |
| jdbc.audit          | ResultSet 을 제외한 모든 JDBC 호출 정보를 로그로 보여줌  |
| jdbc.resultset      | ResultSet 을 포함한 모든 JDBC 호출 정보를 로그로 보여줌  |
| jdbc.connection     | 연결, 연결 해제와 관련된 로그를 보여줌                   |

설명은 위와 같다.

나는 실행시간만 궁금했기 때문에 sqltiming만 info로 설정하였다.

참고로 다른 정보들을 보고싶지 않다면 다른 설정을 다 off 해놓아야 표시되지 않는다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230227213134387.png" alt="image-20230227213134387" style="zoom:80%;" />
</p>

위 사진 처럼 `{excuted in ~~ msec}` 으로 실행 시간이 나타남을 확인할 수 있다.