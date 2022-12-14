---
layout: post
title: "· JdbcTemplate batchUpdate() 사용하여 대용량 데이터 입력 최적화"
nav_order: 10
parent : Spring
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Spring/JdbcBatchUpdate
---

# JdbcTemplate batchUpdate() 사용하여 대용량 데이터 입력 최적화

<br>

임의의 엔티티 `Custom` 이 있다고 가정해보자.

Insert 해야 할 데이터가 많은 경우, Batch 기능을 사용해야하는데,

**DB를 MYSQL 로 사용하고, 엔티티의 기본키 전략이 `IDENTITY` 일 경우 Hibernate에서 Batch Insert를 비활성화 처리하여 사용할 수 없다.**

왜냐하면, `Identity` 전략의 경우, DB에 입력되면서 기본키 값이 부여되는 전략이기 때문이다.

<br>


수십만, 수만건의 데이터를 단일 쿼리로 계속 날린다면... 엄청난 성능저하가 발생할 수 있다.

따라서, 많은 데이터를 입력해여할 경우에는 JDBC 를 사용하면 엄청난 성능 효과를 얻을 수 있다!!



<br>



기존에 JPA의 Repository메서드인 `save`로 단건으로 저장하는 방법은 약 12만건의 데이터를 t3.small 인스턴스에 입력하는데 1시간 가량이 걸렸지만

JDBC를 이용한 방법은 몇초 내로 해결이 되었다...!



<br>



## 구현 예시



<br>



```java
@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Custom {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "custom_id")
    private Long id;

    @Column(name = "custom_name")
    private String customName;

    @Column
    private String size;
}
```

> 위와 같은 `Custom` 엔티티가 있다고 가정해보자. 기본키 전략은 `Identity` 이다.

<br>



```java
@Repository
@RequiredArgsConstructor
public class CustomJdbcRepository {

    private final JdbcTemplate jdbcTemplate;

    public void insertCustomList(List<Custom> customList) {
        jdbcTemplate.batchUpdate("insert into custom (custom_name, size) values " + 
                "(?,?)", new BatchPreparedStatementSetter() {

            @Override
            public int getBatchSize() {
                return customList.size();
            }

            @Override
            public void setValues(PreparedStatement ps, int i) throws SQLException {
                Custom custom = customList.get(i);
                int parameterIndex = 1;
                ps.setString(parameterIndex++, custom.getCustomName());
                ps.setInt(parameterIndex, custom.getSize());
               
        });
    }
}

```

> 위와 같이 `JdbcTemplate` 클래스의 `batchUpdate()` 메서드를 오버라이딩 하면 된다.
>
> 매개변수로서 `Custom` 객체를 담은 리스트를 입력해야하므로, 데이터(txt파일이나 csv 파일)를 파싱해서 객체 리스트로 저장하는 메서드를 정의한 뒤, 사용해야한다.

<br>


{: .warning}
> `insert` 구문 작성 시, 클래스의 변수명이 아닌, DB에 저장되어 있는 속성명을 입력해주어야 한다.
>
> 또한, `PreparedStatement` 에 값을 넣을 때, Type에 맞게 `setString` 혹은 `setInt` 등등을 써주어야 한다.

<br>

```java
public class InsertData {


    public static void main(String[] args) throws IOException {
        Map<String, String> env = System.getenv();
        SimpleDriverDataSource datasource = new SimpleDriverDataSource();
        datasource.setDriverClass(com.mysql.cj.jdbc.Driver.class);
        datasource.setUrl(env.get("SPRING_DATASOURCE_URL"));
        datasource.setUsername(env.get("SPRING_DATASOURCE_USERNAME"));
        datasource.setPassword(env.get("SPRING_DATASOURCE_PASSWORD"));

        JdbcTemplate jdbcTemplate = new JdbcTemplate(datasource);
        CustomJdbcRepository customJdbcRepository = new CustomJdbcRepository(jdbcTemplate);

        ReadData readData = new ReadData();
        List<Custom> customs = readData.readLine("customData.txt");
        customJdbcRepository.insertCustomList(customs);
    }
}

```

> `JdbcTemplate` 클래스를 사용하기 위해, `SimpleDataSource` 클래스도 사용해주어야 한다.
>
> 보안을 위해, DB정보는 환경변수로 입력받는다.

<br>


{: .warning}
> 주의할 점은, `datasource url` 주소 제일 뒤에 `?&rewriteBatchedStatements=true` 를 붙여줘야 한다는 사실이다.
>
> batch 형태의 SQL로 재작성 해주는 옵션이고, 기본값이 false 이기 때문에 true로 설정해야한다.
> 

<br>

{: .important-title}
> 환경변수 예시
> 
> SPRING_DATASOURCE_URL=jdbc:mysql://localhost:3306/inkyu-db?&rewriteBatchedStatements=true;SPRING_DATASOURCE_USERNAME=root;SPRING_DATASOURCE_PASSWORD=12341234