---
layout: post
title: "· Jdbc Template으로 쉽게 DB 활용하기"
nav_order: 8
parent : Spring
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Spring/JdbcTemplate
---

# Jdbc Template으로 쉽게 DB 활용하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Jdbc Template 란?

<br>

스프링은 JDBC를 위한 다양한 메서드를 제공하고 있으며, `JdbcTemplate` 클래스를 생성하면 된다.

파라미터로는 `DataSource` 를 입력해주면 된다.



---





## Jdbc Template 사용

<br>



```java
@Configuration
public class UserDaoConfig {

    @Bean
    public UserDao userDao() {
        return new UserDao(dataSource());
    }

    @Bean
    public DataSource dataSource() {
        Map<String, String> env = System.getenv();

        SimpleDriverDataSource dataSource = new SimpleDriverDataSource();
        dataSource.setDriverClass(com.mysql.cj.jdbc.Driver.class);
        dataSource.setUrl(env.get("DB_HOST"));
        dataSource.setUsername(env.get("DB_USER"));
        dataSource.setPassword(env.get("DB_PASSWORD"));
        return dataSource;
    }


}
```

위와 같은, `DataSource` 객체를 Configuration 에너테이션이 있는 소스파일안에서 스프링 빈 저장소에 의해 입력받게 될 것이다.

`JdbcTemplate` 를 사용하고 싶은 파일에서는,

```java
public class UserDao {

    private final JdbcTemplate jdbcTemplate;
    
    public UserDao(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }
}
```

DI 주입에 의해 입력된 `dataSource` 는 기본 생성자에서 `JdbcTemplate` 의 매개변수로 입력되고, `JdbcTemplate` 객체가 초기화 되어, 다양한 메서드를 사용할 수 있다.



### jdbcTemplate.update()

---

```java
public class UserDao {

	public void insert(final User user) throws ClassNotFoundException, SQLException {
    	    jdbcTemplate.update("INSERT INTO users(id,name,password) values(?,?,?)", 
        	                    user.getId(), user.getName(), user.getPassword());
    	}

	 public void deleteAll() throws SQLException, ClassNotFoundException {
    	    jdbcTemplate.update("delete from users");
    	}
}
```



`jdbcTemplate` 를 사용했더니, 엄청나게 간결해졌다.

`jdbcTemplate.update` 메서드는, 위와 같이 `ResultSet` 이 없는, MYSQL 에 쿼리문만 전달 시켜서 실행시키는 구문을 실행시킬 때 사용하면 된다.

`update("SQL 쿼리문", 쿼리문에 바인딩 할 시 파라미터 )`



### jdbcTemplate.queryForObject()

---

```java
public class UserDao {
    
    RowMapper<User> rowMapper = new RowMapper<User>() {
        @Override
        public User mapRow(ResultSet rs, int rowNum) throws SQLException {
            User user = new User(rs.getString("id"), rs.getString("name"), rs.getString("password"));

            return user;
        }
    };
    
	public int getCount() throws SQLException, ClassNotFoundException {
        String sql = "SELECT COUNT(*) AS COUNT FROM USERS";
        return this.jdbcTemplate.queryForObject(sql, Integer.class);
    }

    public User getById(String id) throws SQLException, ClassNotFoundException {
        String sql = "SELECT * FROM USERS WHERE ID = ?";
        return this.jdbcTemplate.queryForObject(sql, rowMapper, id);
    }
}
```



`queryForObject` 는 SQL 쿼리문 실행 결과로 부터 원하는 데이터를 추출할 수 있다.

기본 사용 방법은 `queryForObject(SQL 구문, 반환타입, 바인딩 할 시 파라미터)` 이다.

예를 들면, `jdbcTemplate.queryForObject("SELECT password FROM USERS WHERE id = ?",String.class,"1234")` 라고 하면, id가 "1234" 인 유저의 패스워드를 String 타입으로 반환받겠다는 의미이다.



따라서, `getCount()` 메서드에서는 `queryForObject(sql,Integer.class)` 로 사용하였고, 이는 `quertForInt(sql)` 로 사용할 수 도 있다.



<br>

`getById()` 메서드에서는 SQL 결과물 정보를 `USER` 객체에 인스턴스 변수에 각각 저장한 뒤, 객체를 반환하기 위해서 `rowMapper` 라는 것을 사용하였다.

`RowMapper` 는 SQL 결과물 테이블에서 행 하나를 맵핑한다고 생각하면 된다. `RowMapper` 는 인터페이스이기 때문에, `@Override` 에너테이션이 있는 `mapRow()` 메서드를 원하는 방식으로 오버라이딩 해주어야 한다. 쿼리문의 결과로 생성되는 `ResultSet` 객체에서 `getString("속성명")` 을 통해서 User 객체의 인스턴스에 데이터를 저장한 후 `User` 를 반환하게 구현하면 된다.





### jdbcTemplate.query()

---

`query()` 메서드는 `ResultSet` 의 모든 로우를 열람하면서, 로우마다 `rowMapper` 로 구현한 `mapRow` 메서드를 사용한다. 따라서, DB에서 가져오는 로우의 개수만큼 호출될 것이다. 그리고, 이러한 결과를 `List` 객체에 담아서 반환해준다.



```java
public class UserDao {
    
    RowMapper<User> rowMapper = new RowMapper<User>() {
        @Override
        public User mapRow(ResultSet rs, int rowNum) throws SQLException {
            User user = new User(rs.getString("id"), rs.getString("name"), rs.getString("password"));

            return user;
        }
    };    
    
	public List<User> getAll(){
        String sql = "SELECT * FROM USERS ORDER BY id";
        return this.jdbcTemplate.query(sql, rowMapper);
    }
}
```



위와 같은 코드를 실행시키면, 쿼리문 실행 결과를 바탕으로 한 데이터를 `User` 객체에 담은 뒤, 그 `User` 객체들을 담고 있는 `List` 객체가 반환되어 `.size()` 나 `.get()` 과 같은 List 메서드를 활용하여 데이터를 다룰 수 있게 된다.