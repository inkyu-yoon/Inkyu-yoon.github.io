---
layout: post
title: "· Querydsl 환경 세팅하기"
nav_order: 5
parent : JPA
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/JPA/QuerydslEnv
---

# Querydsl 환경 세팅하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Querydsl 을 사용해야하는 이유

<br>

- 실무에서는 실제로 동적쿼리를 작성해야 할 일이 엄청나게 많다. 쿼리를 자바 코드로 작성할 수 있게 해주어 문법 오류(컴파일 오류)를 빠르게 찾을 수 있다.

- 이러한 이점이 있는 만큼 실무에서는 거의 필수로 사용해야한다.



## 환경설정 (build.gradle)

<br>

### buildscript 추가

<br>

```groovy
buildscript {
    ext {
        queryDslVersion = "5.0.0"
    }
}
```



###  plugin 추가

<br>

```groovy
// plugin에 querydsl 추가
 id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
```

### dependencies 추가

<br>

```groovy
 // dependencies 에 querydsl 추가
   implementation "com.querydsl:querydsl-jpa:${queryDslVersion}"
   implementation "com.querydsl:querydsl-apt:${queryDslVersion}" 
```

### querydsl 추가

<br>

```groovy
//제일 아래 빈 공간에 querydsl 추가 시작
def querydslDir = "$buildDir/generated/querydsl"
querydsl {
    jpa = true
    querydslSourcesDir = querydslDir
}
sourceSets {
    main.java.srcDir querydslDir
}
configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
    querydsl.extendsFrom compileClasspath
}
compileQuerydsl {
    options.annotationProcessorPath = configurations.querydsl
}
//querydsl 추가 끝
```



최종 `build.gradle`  결과

```groovy
buildscript {
    ext {
        queryDslVersion = "5.0.0"
    }
}

plugins {
    id 'java'
    id 'org.springframework.boot' version '2.7.6'
    id 'io.spring.dependency-management' version '1.0.15.RELEASE'
    id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
}

group = 'jpa'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    runtimeOnly 'com.mysql:mysql-connector-j'
    annotationProcessor 'org.springframework.boot:spring-boot-configuration-processor'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    implementation "com.querydsl:querydsl-jpa:${queryDslVersion}"
    implementation "com.querydsl:querydsl-apt:${queryDslVersion}"
}

tasks.named('test') {
    useJUnitPlatform()
}

def querydslDir = "$buildDir/generated/querydsl"
querydsl {
    jpa = true
    querydslSourcesDir = querydslDir
}
sourceSets {
    main.java.srcDir querydslDir
}
configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
    querydsl.extendsFrom compileClasspath
}
compileQuerydsl {
    options.annotationProcessorPath = configurations.querydsl
}
```





### build 파일 생성

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221128143117365.png" alt="image-20221128143117365" style="zoom:80%;" />
</p>

`complieQuerydsl` 을 더블클릭하여 build 한다.

> 새로운 엔티티 클래스를 생성하고, 이를 Querydsl 활용을 하기 위해서, 위 빌드 과정을 거쳐야한다.