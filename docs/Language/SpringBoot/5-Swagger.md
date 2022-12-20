---
layout: post
title: "· Gradle 프로젝트 swagger-ui 적용"
nav_order: 5
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/Swagger
---


# Gradle 프로젝트 swagger-ui 적용

<br>

## dependecy 추가
<br>

```groovy
    implementation 'io.springfox:springfox-swagger2:2.9.2'
    implementation 'io.springfox:springfox-swagger-ui:2.9.2'
```

<br>

## SwaggerConfig 파일 생성

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class SwaggerConfiguration {
    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Title")
                .version("1.0.0")
                .description("Description")
                .build();

    }
}

```

<br>

## application.yml 추가

<br>

```yaml
spring:
  mvc:
    pathmatch:
      matching-strategy: ant_path_matcher
```

{: .highlight }
위 구문을 꼭 추가해주어야 에러가 발생하지 않는다.

Spring boot 2.6버전 이후에 spring.mvc.pathmatch.matching-strategy 값이 ant_apth_matcher에서 path_pattern_parser로 변경되면서 

오류가 발생되는 것이라고 한다.

<br>

```
Failed to start bean 'documentationPluginsBootstrapper'; nested exception is java.lang.NullPointerException
```

위와 같은 에러가 발생하게 됨.