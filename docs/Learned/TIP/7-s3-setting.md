---
layout: post
title: "·  AWS S3 IAM 사용자 계정 생성 및 엑세스 키 발급받기"
nav_order: 7
parent : TIP
grand_parent: 📚Learned
permalink: docs/Learned/TIP/s3-setting
---

# AWS S3 IAM 사용자 계정 생성 및 엑세스 키 발급받기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---





## AWS S3란?



아마존 S3 (Amazon Simple Storage Service)는 AWS의 클라우드 기반 객체 스토리지 서비스이다.

S3는 무제한의 스토리지 용량을 제공하고 복제 및 백업 기능을 통해 데이터의 내구성과 신뢰성을 보장한다.

클라우드 환경에 보관되어 있어 하드웨어에 문제가 생겨도 데이터 손실이 발생하지 않는다.

RDBMS는 주로 텍스트 기반의 데이터를 조회하는데 최적화되어 있다.

하지만, S3는 이미지 파일과 같은 대용량 데이터를 효율적으로 관리할 수 있는 장점이 있고 보안 설정을 통해 보안을 강화할 수 있다는 장점이 있다.

따라서, S3를 통해서 이미지 파일을 관리해보려고 한다.



S3를 사용하려면 **AWS IAM 사용자를 생성하고 해당 사용자에게 S3에 액세스할 수 있는 권한을 부여**해야 한다.



### IAM 사용자 생성

IAM 사용자를 생성하려면, 우선 루트 사용자 계정으로 로그인한다.

<br>

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605142811944.png" alt="image-20230605142811944" style="zoom:50%;" />
</p>

그리고, 우측 상단에 아이디를 클릭해서 `계정` 을 클릭한다.

<br>

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605143137215.png" alt="image-20230605143137215" style="zoom:67%;" />
</p>

스크롤을 내리다보면, 결제 정보에 대한 IAM 사용자 및 역할 엑세스 라는 부분이 있고

위와 같이, 오른쪽 상단 `편집` 버튼을 눌러 IAM 사용자 엑세스를 활성화한다.

<br>

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605144322460.png" alt="image-20230605144322460" style="zoom:50%;" />
</p>

`IAM` 을 검색해서 IAM 페이지에 접속한다.

<br>

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605144635868.png" alt="image-20230605144635868" style="zoom:80%;" />
</p>

좌측의 `엑세스 관리` 의 `사용자`를 클릭하고 `사용자 추가` 를 클릭한다.

<br>

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605145051455.png" alt="image-20230605145051455" style="zoom:67%;" />
</p>

사용자 이름은 `Administrator` 로 설정했고, `AWS Management Console에 대한 사용자 엑세스 권한 제공` 을 체크하고

`IAM 사용자를 생성하고 싶음` 도 체크한다.

그리고, `사용자 지정 암호`를 설정해주고 `사용자는 다음 로그인 시 새 암호를 생성해야 합니다.` 는 체크 해제 해준 뒤, `다음` 버튼을 클릭한다.

<br>



<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605145350139.png" alt="image-20230605145350139" style="zoom:67%;" />
</p>

`그룹 생성`을 클릭한다.

<br>

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605145536673.png" alt="image-20230605145536673" style="zoom: 50%;" />
</p>

사용자 그룹 이름은 `Administrators` 로, 권한 정책은 `AdministratorAccess` 를 선택해서 그룹을 생성해준다.

`AdministratorAccess` 정책은 모든 리소스에 모든 액세스가 가능한 권한이다.

<br>

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605145735353.png" alt="image-20230605145735353" style="zoom: 67%;" />
</p>

그룹을 생성했으니, `그룹에 사용자 추가` 를 체크하고, 만든 그룹도 체크한 뒤 `다음` 버튼을 누른다.

그리고 `사용자 생성 버튼`을 클릭해서 마무리한다.

<br>

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605150128660.png" alt="image-20230605150128660" style="zoom:50%;" />
</p>

위와 같은 화면이 나타나고, `.csv 파일 다운로드` 를 클릭하면 생성한 계정 로그인 url과 이름, 암호가 담겨있는 파일을 다운 받을 수 있다.

`콘솔 로그인 URL` 에 접속해서 로그인을 하면, 생성한 `Administrator `계정으로 로그인된다.



이제 개발용 IAM 사용자를 생성해서 액세스 키와 비밀 엑세스 키를 발급할것이다.

<br>



<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605150602385.png" alt="image-20230605150602385"  />
</p>



`Administrator ` 사용자로 로그인 상태에서 다시 `IAM` → `사용자` → `사용자 추가` 를 클릭한다.

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605153811279.png" alt="image-20230605153811279" style="zoom:80%;" />
</p>

사용자를 생성한다.

<br>

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605153947129.png" alt="image-20230605153947129"  />
</p>

`직접 정책 연결` 을 선택하고 S3와 관련된 모든 작업을 할 수 있는, `AmazonS3FullAccess` 정책을 선택한다.

그리고 사용자를 생성한다.

<br>



### 엑세스 키 발급


<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605154243261.png" alt="image-20230605154243261" style="zoom: 50%;" />
</p>

방금 생성한 사용자 계정을 클릭한다.

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605154425733.png" alt="image-20230605154425733" style="zoom: 50%;" />
</p>

`보안 자격 증명` 탭에서, 스크롤을 내리면 엑세스 키 발급 관련 정보가 있고 발급받으면 된다.

<br>


<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605154603252.png" alt="image-20230605154603252" style="zoom: 67%;" />
</p>



나는 `기타` 를 클릭해서 엑세스키를 발급 받았다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230605154724496.png" alt="image-20230605154724496" style="zoom: 50%;" />
</p>

위와 같이, 엑세스 키와 비밀 엑세스 키를 발급 받았다.

이 키를 통해서, Spring Boot와 연동할 수 있게 된다.