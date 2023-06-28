---
layout: post
title: "· Spring Boot AWS S3 에 파일 업로드 하기"
nav_order: 22
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/s3-image-upload
---

# AWS S3 에 파일 업로드 하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



S3에 파일을 업로드 하기 위해서는 `버킷` 을 생성해야한다.

그리고, 생성한 `버킷` 에 `파일` 을 업로드할 것이다.

`버킷`을 `디렉토리` 나 `폴더` 정도로 생각하면 된다.



## S3 버킷 생성하기

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605160943016.png" alt="image-20230605160943016" style="zoom:50%;" />
</p>

IAM 관리자 계정으로 로그인한 상태로 `버킷` 을 검색하고, 클릭한다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605161106544.png" alt="image-20230605161106544" style="zoom: 50%;" />
</p>

`버킷 만들기` 버튼을 클릭한다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605161347582.png" alt="image-20230605161347582" style="zoom:67%;" />
</p>


`버킷` 이름은 전 세계적으로 고유해야하며 DNS 호스팅 규칙을 따라 소문자로만 구성되어야 한다.

`AWS 리전` 은 서울로 설정했다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230607151330810.png" alt="image-20230607151330810" style="zoom:67%;" />
</p>

그리고 아래 객체 소유권 옵션은, 위와 같이 ACL을 활성화 시킨다.

```
com.amazonaws.services.s3.model.AmazonS3Exception: The bucket does not allow ACLs (Service: Amazon S3; Status Code: 400;
```

위와 같은 에러가 발생할 수 있기 때문이다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230607153455739.png" alt="image-20230607153455739" style="zoom:67%;" />
</p>


그리고 퍼블릭 엑세스 차단도 해제한다.

```
com.amazonaws.services.s3.model.AmazonS3Exception: Access Denied (Service: Amazon S3; Status Code: 403; Error Code: AccessDenied; 
```

위와 같은 에러가 발생할 수 있기 때문이다.



<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605161533708.png" alt="image-20230605161533708" style="zoom:50%;" />
</p>

업로드 되는 파일을 폴더별로 정리하는 것이 좋으므로 생성한 버킷을 클릭해서 `폴더 만들기` 로 폴더를 생성한다.



<br>



## 상품에 여러개의 상품 이미지를 S3로 업로드 하기



### 환경 세팅



```
implementation 'org.springframework.cloud:spring-cloud-starter-aws:2.2.6.RELEASE'
```

먼저, 라이브러리를 추가한다.

참고로, 나는 `Java 17` `SpringBoot 3.0.5` 를 사용중이다.

<br>

```yaml
cloud:
  aws:
    credentials:
      accessKey: ${AWS_ACCESS_KEY_ID}       # AWS IAM AccessKey
      secretKey: ${AWS_SECRET_ACCESS_KEY}   # AWS IAM SecretKey
    s3:
      bucket: bucket    # 버킷 이름
    region:
      static: ap-northeast-2
    stack:
      auto: false
```



위와 같은 내용을 `application.yml` 에 추가해준다.

엑세스키는 가짜 정보를 두고 보안을 위해 환경변수로 입력받도록한다.

이렇게 해두면, 자동으로 `AmazonS3Client` 빈이 설정정보에 맞게 주입되어 등록된다.

<br>

그리고 만약

```
org.apache.tomcat.util.http.fileupload.impl.FileSizeLimitExceededException: The field multipartFiles exceeds its maximum permitted size of 1048576 bytes.
	at org.apache.tomcat.util.http.fileupload.impl.FileItemStreamImpl$1.raiseError(FileItemStreamImpl.java:117) ~[tomcat-embed-core-10.1.7.jar:10.1.7]
```

파일 업로드시 위와 같은 에러가 발생할 수 있다.

용량 제한 때문에 생기는 에러인데 아래와 같은 부분을 `application.yml`에 추가해주면 된다.

```yaml
spring:
  servlet:
    multipart:
      max-file-size: 50MB
      max-request-size: 50MB
```

<br>

```java
@Configuration
public class AwsS3Config {

    @Value("${cloud.aws.credentials.accessKey}")
    private String accessKey;

    @Value("${cloud.aws.credentials.secretKey}")
    private String secretKey;

    @Value("${cloud.aws.region.static}")
    private String region;

    @Bean
    public AmazonS3Client amazonS3Client() {
        AWSCredentials awsCredentials = new BasicAWSCredentials(accessKey, secretKey);
        return (AmazonS3Client) AmazonS3ClientBuilder.standard()
                .withRegion(region)
                .withCredentials(new AWSStaticCredentialsProvider(awsCredentials))
                .build();
    }

}
```



위와 같이, S3에 접근할 수 있는 권한이 있는 IAM 사용자의 엑세스 키와 시크릿 엑세스 키, 리전을 이용해서 AmazonS3Cilent 빈을 등록할 수 있는 Config 클래스를 만든다.



<br>

### Entity



```java
@Entity
@Getter
@AllArgsConstructor
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Product extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    private Long quantity;

    private Long price;

}

```



```java
@Entity
@Getter
@Builder
@AllArgsConstructor
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class ProductImage extends BaseEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String imageUrl;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "product_id")
    private Product product;


    public static ProductImage of(String imageUrl, Product product) {
        return ProductImage.builder()
                .product(product)
                .imageUrl(imageUrl)
                .build();
    }
}

```



먼저, 나는 상품과 1 대 다 관계를 가진 상품 이미지 테이블을 정의했다.

1대1 관계였다면, 상품 엔티티 안에 칼럼 하나로 관리했겠지만 사진이 여러개 존재하는게 맞다고 생각이 들었고

그렇기 때문에 1 대 다 관계를 맺어야 한다고 생각했다.



### Service 로직



```java
public String upload(MultipartFile file, String bucket, String folder) {

        ObjectMetadata objectMetadata = new ObjectMetadata();
        objectMetadata.setContentType(file.getContentType());
        objectMetadata.setContentLength(file.getSize());

        String originalFileName = file.getOriginalFilename();

        // 파일 형식 체크
        FileUtil.checkFileFormat(originalFileName);

        // 파일 생성
        String key = FileUtil.makeFileName(originalFileName, folder);

        try (InputStream inputStream = file.getInputStream()) {
            amazonS3Client.putObject(new PutObjectRequest(bucket, key, inputStream, objectMetadata)
                    .withCannedAcl(CannedAccessControlList.PublicRead));
        } catch (IOException e) {
            throw new AppException(ErrorCode.FILE_UPLOAD_ERROR);
        }

        String storedFileUrl = amazonS3Client.getUrl(bucket, key).toString();

        return storedFileUrl;
    }
```



코드에서 주목해야할 부분은 `amazonS3Client.putObject()` 이다.

파라미터로 파일을 저장할 `버킷명`, `경로와 파일이름` , `입력 스트림`, `메타데이터` 를 전달하고

`.withCannedAcl(CannedAccessControlList.PublicRead));` 메서드를 통해 파일을 공개 읽기 권한으로 설정한다.

그러면 `amazonS3Client.getUrl(bucket, key).toString();` 을 통해서 해당 파일이 저장된 url 을 조회할 수 있게된다.



```java
    public static void checkFileFormat(String originalFileName) {

        int index;
        try {
            index = originalFileName.lastIndexOf(".");
        } catch(StringIndexOutOfBoundsException e) {
            throw new AppException(ErrorCode.WRONG_FILE_FORMAT);
        }

        String ext = originalFileName.substring(index + 1);
        if(!(ext.equals("jpg") || ext.equals("jpeg") || ext.equals("png") || ext.equals("gif"))) {
            throw new AppException(ErrorCode.WRONG_FILE_FORMAT);
        }
    }

    public static String makeFileName(String originalFileName, String folder) {

        int index = originalFileName.lastIndexOf(".");
        String ext = originalFileName.substring(index + 1);

        String storedFileName = UUID.randomUUID() + "." + ext;

        return folder + "/" + storedFileName;
    }

```

`checkFileFormat` 메서드는 업로드한 파일의 확장자를 기준으로 정상적인 파일인지 확인하는 메서드이다.

`makeFileName`은 버킷과 버킷 내에 따로 생성한 폴더명을 통해서 파일명이 겹치치 않도록 저장되게 구현하였다.

 <br>

```java
    public FileResponse uploadProductFiles(Long productId, List<MultipartFile> multipartFiles) {

        FileUtil.checkFileExist(multipartFiles);

        Product product = productRepository.findById(productId)
                .orElseThrow(() -> new AppException(ErrorCode.PRODUCT_NOT_FOUND));
        
		// ORIGIN_PRODUCT_FOLDER 는 Enum 타입으로 관리한다. 생성한 폴더명을 문자열로 전달해주면 된다.
        multipartFiles.forEach(multipartFile -> {
                    String imageUrl = upload(multipartFile, bucket, ORIGIN_PRODUCT_FOLDER);;
                    productImageRepository.save(ProductImage.of(imageUrl,product));
                }
        );

        return FileResponse.builder().division(ORIGIN_PRODUCT_FOLDER)
                .divisionId(productId)
                .build();
    }
```

그리고, 파일을 `List` 로 받아 처리하는 메서드를 정의한다.

먼저, `List` 의 사이즈가 0인지 확인하는 `checkFileExist()` 메서드로 파일들이 담겨있는지 확인한다.

`ProductImage` 는 `Product` 엔티티와 1 대 다 연관관계가 맺어져 있기 때문에, 전달된 `productId`에 해당하는 데이터가 존재하는지 확인해야한다.

존재한다면, `List` 안에 있는 파일들을 `forEach`를 통해서 이전에 정의했던 `upload()` 메서드를 이용해서 S3에 전달하고 해당 파일이 저장된 url을 조회해온다.

조회한 url과 Product 엔티티를 통해 `ProductImage` 엔티티에도 저장한다.

### Controller 로직

```java
@RestController
@RequestMapping("api/v1")
@RequiredArgsConstructor
public class FileApiController {
    private final AwsS3Service awsS3Service;

    @PostMapping("/products/{productId}/images")
    public ResponseEntity<Response<FileResponse>> uploadProductFiles(Authentication authentication, @PathVariable(name = "productId") Long productId, @RequestPart List<MultipartFile> multipartFiles) {
        FileResponse response = awsS3Service.uploadProductFiles(productId, multipartFiles);
        return ResponseEntity.status(CREATED).body(Response.success(response));
    }
}
```

위와 같이 `Controller` 코드를 작성해준다.

Post 요청을 받아 Service 로직을 실행시켜주는 간단한 코드이다.

`@RequestPart` 어노테이션을 통해 파일 형태로 전달되는 데이터를 `MultiPart` 객체로 매핑해준다.

### 테스트

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230608202135160.png" alt="image-20230608202135160" style="zoom:80%;" />
</p>


Postman을 통해서 테스트를 해보았다.

`Body`의 `form-data` 를 체크하고 원하는 파일을 여러개 선택한 뒤, `Key` 이름을 `multipartFiles`로 설정하고 요청을 보내면


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230608202332642.png" alt="image-20230608202332642" style="zoom:67%;" />
</p>


위와 같이, S3에 저장됨을 확인할 수 있다.



