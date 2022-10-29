---
layout: post
title: "· ExecuteQuery() 메서드 분리하기"
nav_order: 7
parent : Spring
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Spring/ExecuteQuery
---

# ExecuteQuery() 메서드 분리하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## executeUpdate()가 포함된 메서드와 차이점

<br>



```java
public class Dao{

	private ConnectionMaker connectionMaker;

   	 public Dao(ConnectionMaker connectionMaker) {
    	    this.connectionMaker = connectionMaker;
    	}

	public void StatementStrategyUpdate(StatementStrategy stmt) throws SQLException, ClassNotFoundException {
    	    Connection c = null;
        	PreparedStatement ps = null;
      	  try {
     	       c = connectionMaker.makeConnection();
    	        ps = stmt.makePreparedStatement(c);
    	        ps.executeUpdate();
    	    } catch (SQLException e) {
    	        throw e;
    	    } finally {
    	        if (ps != null) {
      	          try {
     	               ps.close();
    	            } catch (SQLException e) {
    	            }
   	         }
   	     }
   	     if (c != null) {
   	         try {
    	            c.close();
   	         } catch (SQLException e) {
  	          }
  	      }
  	  }
}
```



먼저, `Connection c = connectionMaker.makeConnection` 을 통해, `Connection` 객체를 받는다.



```java
public class LocalConnectionMaker implements ConnectionMaker {

    @Override
    public Connection makeConnection() throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.cj.jdbc.Driver");
        Map<String, String> env = System.getenv();

        Connection c = DriverManager.getConnection(env.get("DB_HOST"),env.get("DB_USER"),env.get("DB_PASSWORD"));
        return c;
    }
}
```



`ConnectionMaker` 는  `local` 서버를 이용하는지, `aws` 서버를 이용하는지 등에 따라 `DriverManager.getConnection(String url, String user, String password)` 의 매개변수 값이 달라지기 때문에, `ConnectionMaker`인터페이스를 생성하였고, `LocalConnectionMaker` 로서 구현하여, `local` 환경에 맞게 `Connection` 객체를 생성하여 반환하게끔 만든 메서드 이다.

결론은 `Connection c` 는 위와 같은 메커니즘으로 초기화가 이루어진다.



```java
class DeleteAllStatement implements StatementStrategy {

    @Override
    public PreparedStatement makePreparedStatement(Connection c) throws SQLException {
        PreparedStatement ps = c.prepareStatement("delete from users");
        return ps;
    }
}

class AddStatement implements StatementStrategy{
    User user;

    public AddStatement(User user) {
        this.user = user;
    }
    @Override
    public PreparedStatement makePreparedStatement(Connection c) throws SQLException {
        PreparedStatement ps = c.prepareStatement("INSERT INTO users(id,name,password) values(?,?,?)");
        ps.setString(1, this.user.getId());
        ps.setString(2, this.user.getName());
        ps.setString(3, this.user.getPassword());

        return ps;
    }
}

public class CountStatement implements StatementStrategy {

    @Override
    public PreparedStatement makePreparedStatement(Connection c) throws SQLException {
        PreparedStatement ps = c.prepareStatement( "SELECT COUNT(*) as count FROM users");
        return ps;
    }
}
```



`StatementStrategy` 인터페이스 역시, 쿼리문에 `DELETE` 를 할지 `INSERT` 를 할지에 따라 전달해야하는 구문이 다르기 때문에 만든 인터페이스이고, 각 필요한 명령에 따라 구현 클래스를 만들었다.



따라서 `StatementStrategyUpdate` 메소드가 들어있는`Dao` 객체를 생성할때, 필요한 `ConnectionMaker` 구현 클래스를 생성자로 입력받고, `StatementStrategy`는 메서드의 매개변수로서 필요한 구현클래스를 입력받는다.


{: .note-title }
> 정리
>
> 1. ConnectionMaker의 makeConnection 메서드를 통해서, 환경에 맞는 Connection 객체를 초기화한다.
> 2. StatementStrategy는 매개변수로서 입력받고, 해당 객체 안에 필요한 SQL 쿼리문(DELETE or INSERT)이 PreparedStatement로 작성되어있기 때문에, PreparedStatement 객체를 초기화한다.
> 3. 입력받은 Connection 객체와 PreparedStatement로 .executeUpdate() 메서드를 통해서 MYSQL에 전달하고 SQL 쿼리문(DELETE or INSERT)을 실행한다.
> 4. 중간에 리소스를 close하지 못해서 서버가 다운되는 상황이 벌어지지 않게 try-catch-finally 문으로 안정적으로 리소스를 반환하게 구현하였다.





- 단순히 쿼리문을 `MYSQL` 에 전달하여 실행시키는 `executeUpdate` 과정은 구현하는데 어려움이 없었다. 하지만, 쿼리의 `SELECT` 과정은 `executeQuery` 문을 통해서 `ResultSet` 객체를 반환받아야 하기 때문에 까다로운 점들이 많이 생긴다.

---

## executeQuery() 분리하기

<br>

`SELECT COUNT(*) as count FROM users` 을 `MYSQL` 에 전달하는 `StatementStrategy`를 구현한 `CountStatement ` 을 통해 `PreparedStatement` 객체를 생성하고, 이 객체를  `excuteQuery` 를 통해 `ResultSet` 을 반환받은 후, rs를 다루면 되는 간단한 문제일 줄 알았다.

그래서 아래와 같이 구현했는데, 여러가지 문제가 발생했다.





### 1. ResultSet 반환 시 에러가 발생!!

```java
public ResultSet StatementStrategyForExecute1(StatementStrategy stmt) throws SQLException, ClassNotFoundException {
        ResultSet rs=null;
        Connection c = null;
        PreparedStatement ps = null;

        try {
            c = connectionMaker.makeConnection();
            ps = stmt.makePreparedStatement(c);
            rs = ps.executeQuery();
			return rs;
        } catch (SQLException e) {
            throw e;
        } finally {
            if (ps != null) {
                try {
                    ps.close();
                } catch (SQLException e) {
                }
            }
            if (c != null) {
                try {
                    c.close();
                } catch (SQLException e) {
                }
            }            
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                }
            }
  
        }
    }

public int getCount() throws SQLException, ClassNotFoundException {
        StatementStrategy st = new CountStatement();
        ResultSet rs = StatementStrategyForExecute(st); //위 클래스로부터 ResultSet을 받음
        rs.next();
  		int count = rs.getInt("count");
        return count;
    }
```



위 코드를 테스트 코드를 작성하여 실행시켜봤더니



```
java.sql.SQLException: Operation not allowed after ResultSet closed
```



위와 같은 오류가 발생했다.



`ResultSet` 이 이미 `closed` 되어 `getCount` 메서드 안의 `rs.next()` 메서드를 실행할 때, 오류가 발생했다고 했다.



그래서, 아 `StatementStrategyForExecute` 메서드에서 `ResultSet` 을 `return rs` 가 되고 아래의 `rs.close` 과정이 수행이 안되서 그런가?? 싶어서 찾아보니

<br>

**`try-catch-finally` 문법에서는 `try` 에서 `return` 문이 있어도, `catch` 문에 `return` 문에 있어도 `finally` 구현부는 정상적으로 작동하는 것을 확인했다.**



그러면,  `StatementStrategyForExecute` 메서드에서 `rs.close` 가 실행되서 문제가 생기는 거구나 싶어서 코드를 리팩토링했다.



```java
public ResultSet StatementStrategyForExecute(StatementStrategy stmt) throws SQLException, ClassNotFoundException {
        ResultSet rs=null;
        Connection c = null;
        PreparedStatement ps = null;

        try {
            c = connectionMaker.makeConnection();
            ps = stmt.makePreparedStatement(c);
            rs = ps.executeQuery();
            return rs;
        } catch (SQLException e) {
            throw e;
        } finally {
            if (ps != null) {
                try {
                    ps.close();
                } catch (SQLException e) {
                }
            }
            if (c != null) {
                try {
                    c.close();
                } catch (SQLException e) {
                }
            }
            
        }
    }

 public int getCount() throws SQLException, ClassNotFoundException {
        StatementStrategy st = new CountStatement();
        int count;
        ResultSet rs = StatementStrategyForExecute1(st);
        try {
            rs.next();
            count = rs.getInt("count");
            return count;
        } catch (SQLException e) {

        } finally {
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                }
            }
            return -1;
        }
    }
```



위와 같이 코드를 작성해보고, 테스트 코드를 실행시켜보았더니, `rs.next()` 부분에서

```
Operation not allowed after ResultSet closed
```

역시나 위와 같은 에러가 발생해서 `-1`이 `return` 되고 있었다.



그래서, 인터넷을 찾아보니, `ResultSet` 객체는 `return` 문을 거치면 자동으로 `close` 된다는 사실을 알게되었다.

하...



`ResultSet` 반환은 실패하게 될 것인가...



하지만, 탈출구가 존재했다.



바로 `executeQuery` 를 실행하는 메서드 안에서 `ResultSet` 을 `ArrayList` 로 옮긴 후, `ArrayList`로 반환받아 필요한 데이터를 뽑아내는 방법이 있었다!

심지어, 칼럼에 해당하는 값의 자료형에 관계 없이 데이터를 받을 수 있는 방법이 있다.

<br>

### 2. ArrayList 에 ResultSet을 저장하자!!

```java
public ArrayList<HashMap<String, Object>> StatementStrategyForExecute(StatementStrategy stmt) throws SQLException, ClassNotFoundException {
        ResultSet rs = null;
        Connection c = null;
        PreparedStatement ps = null;
        ArrayList<HashMap<String, Object>> list = new ArrayList<>();
        try {
            c = connectionMaker.makeConnection();
            ps = stmt.makePreparedStatement(c);
            rs = ps.executeQuery();
            ResultSetMetaData md = rs.getMetaData();
            System.out.println("md = " + md);
            int columns = md.getColumnCount();
            System.out.println("columns = " + columns);
            while (rs.next()) {
                HashMap<String, Object> row = new HashMap<>(columns);
                for (int i = 1; i <= columns; i++) {
                    row.put(md.getColumnName(i), rs.getObject(i));
                }
                list.add(row);
            }

            return list;
        } catch (SQLException e) {
            throw e;
        } finally {
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                }
            }
            if (ps != null) {
                try {
                    ps.close();
                } catch (SQLException e) {
                }
            }
            if (c != null) {
                try {
                    c.close();
                } catch (SQLException e) {
                }
            }
        }
    }

public int getCount() throws SQLException, ClassNotFoundException {
        StatementStrategy st = new CountStatement();
        ArrayList<HashMap<String, Object>> list = StatementStrategyForExecute(st);
        System.out.println(list);
        return Integer.valueOf(String.valueOf(list.get(0).get("count")));
    }
```



먼저, 구현 코드는 위와 같다. 리소스를 반환하는 `try-catch-finally` 문을 생략하고 구현부를 제대로 살펴보겠다.



```java
public ArrayList<HashMap<String, Object>> StatementStrategyForExecute(StatementStrategy stmt) throws SQLException, ClassNotFoundException {
        ResultSet rs = null;
        Connection c = null;
        PreparedStatement ps = null;
        ArrayList<HashMap<String, Object>> list = new ArrayList<>();
 
        c = connectionMaker.makeConnection();
        ps = stmt.makePreparedStatement(c);
        rs = ps.executeQuery();
        ResultSetMetaData md = rs.getMetaData();
        int columns = md.getColumnCount();

        while (rs.next()) {
            HashMap<String, Object> row = new HashMap<>(columns);
            for (int i = 1; i <= columns; i++) {
                row.put(md.getColumnName(i), rs.getObject(i));
            }
            list.add(row);
        }
        return list;
}
```



먼저 `ResultSet` 의 데이터를 옮길 `ArrayList<HashMap<String,Object>> list`  를 준비한다.


<p align = center>
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221021174403337.png" alt="image-20221021174403337" style="zoom: 50%;" />
</p>

위와 같은 방식으로 데이터를 저장할 것이다. `ArrayList` 각 원소들은 쿼리문 결과물인 `속성(칼럼명)` 과 `데이터` 를 지닌` Map`으로 이루어져 있다.

<p align = center>
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221021174832251.png" alt="image-20221021174832251" style="zoom:50%;" />
</p>

내 코드는 상황은 위와 같은 `ArrayList`를 만들어  `list.get(0).get("count")` 했을때, 원하는 데이터가 나올 것이다. 물론, 데이터가 항상 `Integer` 가 아니기 때문에, 범용성을 위해서 `Object` 타입으로 받을 것이다.



```
ResultSetMetaData md = rs.getMetaData();
int columns = md.getColumnCount();
```



`ResultSetMetaData` 객체를 `getMetaData()` 메서드를 통해 생성할 수 있다.

유용한 메서드들을 많이 제공하는데

1. getColumnName() : 속성의 이름을 반환
2. getColumnType() : 속성의 타입을 반환
3. getColumnCount() : ResultSet의 총 속성 수를 반환
4. isReadOnly() : 읽기만 가능한 필드인지 알려준다.

이러한 메서드 들을 제공한다.

따라서 `getColumnCount` 메서드로 속성의 갯수를 파악하고 `columns` 변수에 넣었다.



```java
			while (rs.next()) {
                HashMap<String, Object> row = new HashMap<>(columns);
                for (int i = 1; i <= columns; i++) {
                    row.put(md.getColumnName(i), rs.getObject(i));
                }
                list.add(row);
            }
		return list;
```



`columns` 를 통해 `HashMap`의 초기 용량을 지정하였고, `getColumnName` 과 `ResultSet`의 `getObject` 메서드를 통해서 리스트에 `add` 해준다.

(HashMap은 저장할 데이터의 갯수를 알고 있을 시 초기 용량을 지정해주는 것이 좋다.)

결과적으로 원하는 방식으로 데이터를 채운 list를 반환한다.

```java
public int getCount() throws SQLException, ClassNotFoundException {
        StatementStrategy st = new CountStatement();
        ArrayList<HashMap<String, Object>> list = StatementStrategyForExecute(st);
        System.out.println(list);
        return Integer.valueOf(String.valueOf(list.get(0).get("count")));
    }
```

그리고 `Object` 값을 `int` 형으로 바꾸기 위해서는, `String` 으로 변환해준 뒤, 다시 `Integer` 로 변환해주면 된다!



## 테스트

---

<br>



```java
 @Test
    @DisplayName("deleteAll & getCount 테스트")
    void 삭제랑카운트() throws SQLException, ClassNotFoundException {

        uDao.add(new User("Id1", "inkyu", "12341234"));
        Dao.add(new User("Id2", "inkyu", "12341234"));
        assertThat(Dao.getCount()).isEqualTo(2);
        // 카운트메서드 테스트

        Dao.deleteAll();
        assertThat(Dao.getCount()).isEqualTo(0);
    }
```



기능이 잘 동작하는지 테스트 코드를 작성하였다. 그리고 리스트를 `getCount()` 를 사용할때마다 출력해보았다.


<p align = center>
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221021180520739.png" alt="image-20221021180520739" style="zoom:80%;" />
</p>


첫번째, `getCount` 메서드에서는 count 값이 2개로 잘 입력되었고, 두번째 `getCount` 메서드에서는 0개로 잘 입력되었으며, 테스트도 성공했다. ㅎㅎ