---
layout: post
title: "· 팀원들과 함께 만들어본 코드 컨벤션과 커밋 규칙"
nav_order: 3
parent : 🤖Project
permalink: docs/Project/CodeConvention
---

# 팀원들과 함께 만들어본 코드 컨벤션과 커밋 규칙

{: .no_toc }

## Table of contents

{: .no_toc .text-delta }

1. TOC
   {:toc}

---

{: .highlight }
팀 프로젝트에 들어가기 앞서, 효율적인 협업과 통일성을 위해 여러가지 자료를 참고해서 코드 컨벤션을 만들어 보았다. 참고로 아직 미완성이라 수정될 수 있다.

## 📌 줄바꿈, 공백, (콤마· 조건문 혹은 메서드 괄호 위치) 등등

<br>

### 띄어쓰기, 들여쓰기 등등

<br>

```
ctrl + alt + l
```

팀원 모두가 IntelliJ의, 위 단축키를 활용해서 통일성을 지킨다.

<br>

### `.` 을 사용하는 메서드의 경우 줄바꿈

<br>

`.`으로 이어지는, 메서드의 경우 `.` 을 기준으로 줄바꿈을 한다.

```java
// Good Examples
Post post=Post.builder()
        .title(request.getTitle())
        .build();

//Bad Examples
        Post post=Post.builder().title(request.getTitle()).build();

// Good Examples
        when(postRepository.save(any()))
        .thenReturn(mockPost);

//Bad Examples
        when(postRepository.save(any())).thenReturn(mockPost);
```

<br>

### 변수 선언

<br>

- 모든 변수 선언은 한줄에 한개만 선언한다.

```java
// Good Examples
int a;
        int b;

// Bad Examples
        int a,b;
```

- 지역 변수는 습관적으로 블록의 시작 부분에 선언되는 경향이 있다. 하지만, 지역 변수의 범위를 최소화 하기 위해 처음 사용되는 지점에 가깝게 선언한다.

> 지역변수 : 메소드나 생성자, 초기화 블록 내에 위치한 변수

<br>

## 📌 명명 규칙

<br>

- 한국어 발음대로의 표기를 금지한다.

```java
// Good Examples
public class Hello {
}

// Bad Examples
public class Annyeong {
}
```

- 글자의 길이는 20자 이내로 제한한다. 4 단어 이상이 들어가거나, 부득이하게 20자 이상이 되는 경우 팀원과의 상의를 거친다.

<br>

### 패키지 이름은 소문자로 구성한다.

<br>

- 언더 스코어나 대문자를 섞지 않는다.

```java
// Good Examples 
package com.project.restcontroller

// Bad Examples 
package com.project.restController
        package com.project.rest_controller
```

<br>

### DTO 클래스 명

<br>

- Request Dto 의 경우 [도메인]+[동사]+[Req] 로 작성한다.
- Response Dto 의 경우 [도메인]+[동사]+[Res] 로 작성한다.

```java
// Good Examples 
public class CommentCreateReq;

public class CommentCreateRes;

// Bad Examples 
public class CommentCreateRequest;

public class CommentCreateResponse;
```

<br>

### 클래스, 인터페이스 이름

<br>

- UpperCamelCase를 적용하고, 명사를 사용한다.

```java
// Good Examples 
public class Reservation;

public class AccessToken;

// Bad Examples 
public class reservation;

public class Accesstoken;
```

<br>

### 테스트 클래스의 이름은 [테스트할 클래스 이름 + 'Test'] 으로 짓는다.

<br>

```java
// PostController 테스트 파일 Good Examples 
public class PostControllerTest {
```

<br>

### 변수명

<br>

- 복합 단어일 경우 소문자로 시작한다. (lowerCamelCase를 적용한다.)

```java
//변수 이름 Good Examples 
String userAccount
        String userName

//변수 이름 Bad Examples 
        String UserAccount
        String username
```

<br>

- 일반적인 변수의 이름은 타입의 이름과 동일하게 지정한다.

```java
// Good Examples 
void setTopic(Topic topic)

// Bad Examples 
        void setTopic(Topic value)이 아님
        void setTopic(Topic aTopic)이 아님

// Good Examples 
        void connect(Database database)

// Bad Examples 
        void connect(Database db)가 아님
        void connect(Database oracleDB)가 아님
```

<br>

- 컬렉션의 이름은 반드시 복수형으로 작성한다.

```java
// Good Examples 
List<> students
        Page<> posts
```

<br>

- boolean 변수 혹은 메서드에 `is` 접두사를 붙이고 동사가 사용된다면, 과거형으로 작성한다. (`has` 혹은 `can` 접두사가 더 어울리는 변수명이면 적합한 것으로 사용한다.)
- 부정적인 이름을 사용하지 않는다.

```java
// Good Examples 
boolean isDeleted
// 위 경우에 삭제되었으면 true를 반환해야 한다.

        boolean isFound
        boolean hasLicense

// Bad Examples 
        boolean isDelete
        boolean deleted
        boolean isNotDeleted
        boolean isNotFound
```

<br>

### 메서드명

<br>

- 메서드 이름은 반드시 동사로 시작한다.
- controller 메서드 이름의 경우 동작을 대표할 수 있는 복합어를 사용하고, service 메소드 이름은 domain을 controller 메소드 이름 뒤에 붙인다.

```java
/* 예시 1 */
// Post Controller 메소드 이름 
create()
// Post Service 메소드 이름 
        createPost()

        /* 예시 2 */
// User Controller 메소드 이름 
        changeRole()
// User Service 메소드 이름 
        changeRoleUser()
```

<br>

### 상수명

<br>

- 상수(인스턴스가 절대 변화하지 않는) 이름은 대문자로 작성한다.

```java
// Constants
static final int NUMBER=5;
```

<br>

### 데이터 베이스 명명 규칙

<br>

- 테이블 명의 구성은 최대 3 단어까지 사용하고 단수형을 사용한다.

- 테이블임을 표시하기 위해서 `_TB` 라는 구분을 사용한다.

  → `@Table` 어노테이션으로 `_TB` 를 추가한 이름을 붙여준다.

```java

@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Table(name = "Example_TB")
public class Example {
}
```

<br>

- 기본키는 다음과 같이 `@Column` 어노테이션을 사용해서 `테이블명_id` 로 속성명을 지정해주고, `Long` 타입을 사용한다.

```java
public class Example {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "Example_id")
    private Long id;

}
```

<br>

- 특이사항

```java
boolean 관련 속성 일때 _flag

        date 관련 속성 일때,_date

        count 조회수 등 속성일 때 _count

//examples

// 값이 true 일때, 삭제 되어있는 데이터 
@Column(name = "delete_flag")
private boolean isDeleted;

@Column(name = "deleted_date")
private Timestamp deletedAt;

@Column(name = "view_count")
private Long views;
```

그외, 속성명은 자바에서 camelCase로 작성하면, JPA auto DDL 시, snake로 변경해서 적용해주는대로 사용한다.

<br>

## 📌 주석 처리

<br>

- 프로그램의 이해를 도와 향후 유지보수가 용이하게 한다.

- 각 Method 앞에 Method의 Parameter, Return Value의 의미와 Method의 간략한 설명을 기술한다.

- 메소드 전체 기능에 대한 설명은 아래와 같은 주석으로 설명한다. `/**` 로 시작해서 `*/` 로 종결되는 주석을 사용한다.

- 메소드 내부 로직에 대한 보충 설명은 설명이 필요한 구문 위쪽에 `//`주석을 활용한다.

    - 예시 (인텔리제이 window 기준, 메서드 위에서 `/**` 를 입력하고 crtl+shift+enter 입력시 레이아웃이 자동완성된다.)
        <p align="center">
         <img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230115162717075.png" alt="image-20230115162717075" style="zoom:80%;" />
        </p>

<br>

## 📌 그 외 규칙

<br>

1. 엔티티 클래스 `Setter` 사용 금지 → 단, Dto 클래스에서는 사용해도 무관하다.
2. `@Builder` 패턴은 필드가 4개이상인 클래스에서만 사용하고 그 외는 생성자 방식을 사용한다.

<br>

## 📌 커밋 규칙

<br>

- [feat] : 새로운 기능의 추가
- [fix] : 버그 수정 , 기능관련 수정 (안 쓰는 변수 추가)
- [docs] : 문서 수정 (e.g readme.md, file)
- [style] : 스타일 관련 기능
    - 코드 포맷팅
    - 세미콜론 누락
    - 코드 자체의 변경이 없는 경우
    - 주석수정
    - 변수명
    - 안쓰는 import정리
- [refactor] : 코드 리펙토링
- [test] : 테스트 코트, 리펙토링 테스트 코드 추가
- [chore] : 빌드 업무 수정, 패키지 매니저 수정 (e.g .gitignore, application.yml)
- [build] : 빌드 관련 수정 (e.g build.gradle)
- [ci] : CI 관련 설정 수정 (e.g .gitlab-ci.yml)
- [ui] : ui의 기능, 수정

<br>

```java
// 예시
[feat]<공백>:<공백>한글로 or 영어로?
<한칸띄우기>
1.커밋 설명
<한칸띄우기>
2.커밋 설명
        ....
```

<br>

### 커밋 규칙 관련

<br>

- 브랜치따서 기능을 구현하고 Main Merge Request 할 때, Git Squash 기능을 사용한다.
- 각자 브랜치에서 기능을 구현할 땐, 디테일하게 커밋내역을 남긴다.