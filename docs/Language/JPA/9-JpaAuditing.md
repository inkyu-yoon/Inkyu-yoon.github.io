---
layout: post
title: "· JPA BaseEntity Auditing 적용"
nav_order: 9
parent : JPA
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/JPA/JpaAuditing
---
# JPA BaseEntity Auditing 적용
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

## @EnableJpaAuditing 적용

<br>

'생성일자', '변경일자' 와 같은 누가, 언제 데이터를 생성했고 변경했는지에 대한 데이터는 엔티티에 공통적으로 많이 사용된다.

이러한 값을 자동으로 넣어주는 기능을 JPA는 제공한다.

<br>

```
@SpringBootApplication
@EnableJpaAuditing
```

`@SpringBootApplication` 이 있는 클래스에 `@EnableJpaAuditing` 어노테이션을 추가하면 된다.

위와 같은 방법은 테스트 코드 작성 시, 애플리케이션 클래스를 호출하는 과정에서 예외가 발생할 수 있다는 단점이 있다.

<br>

```java
@Configuration
@EnableJpaAuditing
public class JpaAuditingConfiguration{

}
```

위와 같이 Configuration 클래스를 하나 만드는 방법이 있다.

<br>



## BaseEntity 생성

```java
@Getter
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public class BaseEntity {

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;
            
}
```

> - `@MappedSuperclass` : 상속받는 엔티티 클래스에 매핑 정보를 전달
>
> - `@EntityListeners` : 데이터베이스에 적용하기 전 후로 콜백을 요청
>
> - `AuditingEntityListener` : 엔티티의 Auditing 정보를 주입하는 JPA 엔티티 리스너 클래스
>
> - `@CreatedDate` : 데이터 생성 날짜 자동으로 주입
>
> - `@LastModifiedDate` : 데이터 수정 날짜 자동으로 주입

<br>

## 리뷰 Entity에 적용

```java
@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@ToString(callSuper = true)
public class Review extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "review_id")
    private Long id;

    @Column(nullable = false)
    private String title;
    @Column(nullable = false, length = 500)
    private String content;
    @Column(name = "user_id", nullable = false)
    private String userId;

    @ManyToOne(fetch = FetchType.LAZY, cascade = CascadeType.ALL)
    @JoinColumn(name = "hospital_id")
    private Hospital hospital;

    public Review(ReviewCreateRequest reviewCreateRequest,Hospital hospital) {
        this.title = reviewCreateRequest.getTitle();
        this.content = reviewCreateRequest.getContent();
        this.userId = reviewCreateRequest.getUserId();
        this.hospital = hospital;
    }
}
```

> - `callSuper = true` 부모 클래스 필드 포함하는 역할 수행

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221208114816833.png" alt="image-20221208114816833" style="zoom:80%;" />
</p>

리뷰 DB 등록 시 잘 적용되는 것을 볼 수 있다.