---
layout: post
title: "· 데이터베이스 데이터 GetMapping 하기"
nav_order: 2
parent : Docker
grand_parent: 📚Learned
permalink: docs/Learned/Docker/DBMSGetMapping
---

# 도커로 서버열고, 데이터베이스 데이터 GetMapping 하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



[Git Bash로 Docker 설치하고 MYSQL 연결하기](https://inkyu-yoon.github.io/docs/Learned/OddsAndEnds/Docker)



위의 과정을 통해서 ec2 서버를 열고, `docker` 를 설치하고, `mysql` 에 연결시킨 후, `hospital` 테이블을 만들어 데이터를 집어넣었다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102192050064.png" alt="image-20221102192050064" style="zoom:67%;" />
</p>

이 데이터베이스에 있는 데이터를 `Getmapping`과 `@Pathvariable` 이용해서 `url`로 요청받아 해당 `id`에 해당하는 데이터를 띄워주는 작업을 해보겠다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102192801906.png" alt="image-20221102192801906" style="zoom:80%;" />
</p>

먼저 인바운드 규칙에 `사용자 지정 TCP` 에 `8080` 포트를 추가해주어야 한다.

해당 인스턴스에 도커까지 설치되어있는 것을 가정하고 진행해보겠다.

---

## Maven 프로젝트 연결

<br>

먼저, 메이븐 프로젝트로 `GetMapping` 을 구현해야한다.

나는 `@Pathvariable` 을 활용해서 `id` 를 입력받으면, 그 `id`에 해당하는 객체를 JPA를 활용하여 반환하게끔 구현하였다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.2"
             xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name="hospital">
        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="com.mysql.cj.jdbc.Driver"/>
            <property name="javax.persistence.jdbc.user" value="user이름"/>
            <property name="javax.persistence.jdbc.password" value="user패스워드"/>
            <property name="javax.persistence.jdbc.url" value="jdbc:mysql://ec2인스턴스주소/db이름"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.MySQLDialect"/>

            <!-- 옵션 -->
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.format_sql" value="true"/>
            <property name="hibernate.use_sql_comments" value="true"/>
            <!--<property name="hibernate.hbm2ddl.auto" value="create" />-->
        </properties>
    </persistence-unit>
</persistence>
```

위는, `META-INF` 폴더에 있는 `persistence.xml` 파일이다.

`user이름` , `user패스워드`, `인스턴스 주소와 db이름`을 자신의 것으로 입력하면 된다.

```java
@RestController
@RequestMapping("/api/v1/hospital")
public class HospitalController {

    //jpa로 넣어놓은 데이터 jpa로 get 하기
    @GetMapping("/{id}")
    public ResponseEntity<HospitalJPA> getByJpa(@PathVariable(value = "id") int id) {
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hospital");
        EntityManager em = emf.createEntityManager();
        EntityTransaction ts = em.getTransaction();

        ts.begin();
        HospitalJPA hospitalJPA = null;
        try {
            hospitalJPA = em.find(HospitalJPA.class, id);
            ts.commit();
        } catch (Exception e) {
            //에러 생기면 트랜잭션 시작하기 전 상황으로 되돌리기
            ts.rollback();
        } finally {
            //em이 비정상적인 오류가 생겨도 닫힐 수 있어야 함
            em.close();
        }
        emf.close();
        Optional<HospitalJPA> opt = Optional.of(hospitalJPA);
        if (!opt.isEmpty()) {
            return ResponseEntity.ok().body(hospitalJPA);
        } else {
            return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(new HospitalJPA());
        }

    }
}

```



Controller는 위와 같이 구성하였다. `ec2주소:8080/api/v1/hospital/검색아이디` 를 입력하면, 해당하는 정보가 나올 것이다.

위와 같은 기능을 구현한 메이븐 프로젝트를 깃허브에 커밋해야한다.

[https://github.com/inkyu-yoon/LikeLion_Backend_School](https://github.com/inkyu-yoon/LikeLion_Backend_School)

나는 위 레포지토리에 `Hospital_Korea` 라는 이름으로 올려두었다.

그리고, 프로젝트 최 상단에 `Dockerfile`이라는 이름의 파일을 생성하고 (확장자 없는)

```
FROM openjdk:11-jdk-slim
VOLUME /tmp
ADD /target/*.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]

```

위 내용을 집어넣는다.



---

## Git Bash Maven 설치 및  docker 서버 열기

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102194144679.png" alt="image-20221102194144679" style="zoom:80%;" />
</p>

먼저, 위와 같은 상태라면 `cd ~` 를 입력하여 최 상단 위치로 이동한다.

그리고, 메이븐 프로젝트가 있는 깃허브 주소를 클론한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102195628222.png" alt="image-20221102195628222"  />
</p>

그리고 나의 경우 만든 프로젝트가 `Hospital_Korea` 폴더에 있으므로 `cd` 를 이용해서 이동하겠다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102195758027.png" alt="image-20221102195758027" style="zoom:150%;" />
</p>

이동한 뒤, 먼저 메이븐을 설치해야한다.

```
apt update
apt install maven
```

을 입력해서 설치한다.



설치한 뒤 `mvn -DskipTests=true package` 를 입력해서 maven 패키지를 먼저 빌드한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102200525424.png" alt="image-20221102200525424" style="zoom:150%;" />
</p>

빌드가 잘 되었다.

이제 도커를 빌드한다.

`docker build -t springboot-jdbc-template . ` 를 입력하면 된다. 마지막에 `.` 을 꼭 붙여주고 공백 한칸을 주어야 한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102201009599.png" alt="image-20221102201009599"  />
</p>

잘 실행되었다.

이제 서버를 시작하면 된다.

```
docker run -p 8080:8080 -e SPRING_DATASOURCE_URL=jdbc:mysql://데이터베이스ec2주소:3306/db명 -e SPRING_DATASOURCE_PASSWORD=데이터베이스비밀번호 springboot-jdbc-template
```

위와 같이 입력하면 된다.

> 만약, 데이터를 넣어둔 데이터베이스 ec2주소가 서버를 여는 주소와 같다면, 똑같이 입력하면 되고, 데이터베이스 ec2주소가 다르다면 위 구문에서, **데이터베이스와 연결되어 있는 주소**를 넣어야 한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102201400358.png" alt="image-20221102201400358" style="zoom:80%;" />
</p>

서버가 잘 열렸다.



---

## url 입력해서 확인해보기

<br>

url 창에 `ec2주소:8080/api/v1/hospital/검색id` 를 입력해서 확인해보겠다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102201713860.png" alt="image-20221102201713860" style="zoom:80%;" />
</p>

`http://ec2-3-112-208-71.ap-northeast-1.compute.amazonaws.com:8080/api/v1/hospital/12345` 를 입력해보니 데이터가 잘 출력된다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221102201801359.png" alt="image-20221102201801359" style="zoom:80%;" />
</p>

110000번째 데이터도 잘 출력되는 것을 확인할 수 있다!

<br>

Short Cut
{: .label .label-yellow }

```
ssh ubuntu@ec2주소 -i ~/Downloads/20221012.pem

sudo su -

bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"

mkdir git

cd git

git clone https://github.com/inkyu-yoon/docker_install

cd docker_install

sh docker_install.sh

docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password -d mysql

cd~ 

git clone https://github.com/inkyu-yoon/LikeLion_Backend_School
 
cd LikeLion_Backend_School 

cd Hospital_Korea 

apt update 

apt install maven 

mvn -DskipTests=true package 

docker build -t springboot-jdbc-template . 

docker run -p 8080:8080 -e SPRING_DATASOURCE_URL=jdbc:mysql://데이터베이스ec2주소:3306/inkyu-db -e SPRING_DATASOURCE_PASSWORD=데이터베이스비밀번호 springboot-jdbc-template


```