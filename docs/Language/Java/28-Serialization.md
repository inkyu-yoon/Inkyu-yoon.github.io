---
layout: post
title: "·  JAVA 직렬화(Serialization)과 역직렬화(Deserialization)"
nav_order: 28
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/Serialization
---

#  JAVA 직렬화(Serialization)과 역직렬화(Deserialization)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



## 직렬화(Serialization)과 역직렬화(Deserialization)의 정의



✅ 직렬화 : 객체들의 데이터를 연속적인 데이터(스트림)로 변형하여 전송 가능한 형태로 만드는 것

✅ 역직렬화 : 직렬화된 데이터를 다시 객체의 형태로 만드는 것



***객체 데이터를 통신하기 쉬운 포멧(Byte,CSV,Json..) 형태로 만들어주는 작업을 직렬화***라고 볼 수 있고,

역으로, ***포멧(Byte,CSV,Json..) 형태에서 객체로 변환하는 과정을 역직렬화***라고 할 수 있겠다.

<br>

```java
class Person{
	private String name;
    
    public Sample(String name) {
        this.name = name;
    }
}

```

위와 같은 클래스가 있다고 할 때, Json 데이터 형식을 예로 들면

`Person person = new Person("김철수");` 객체를 `{ "name" : "김철수"} `와 같은 방식으로 변경하는 것을 직렬화,

`{ "name" : "김철수"}` 데이터를 받아서 `Person`이라는 객체의 `name` 필드에 `"김철수"` 를 할당하고 객체를 생성하는 것을 역직렬화라고 할 수 있다.

<br>

## 직렬화가 필요한 이유?




자바에는 원시타입(Primitive Type)이 byte,short,int,long,float,double,boolean,char 총 8가지가 있다.

그리고 그 외 객체들은 주소값을 갖는 참조형 타입이다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230414201817935.png" alt="image-20230414201817935" style="zoom: 50%;" />
</p>

원시타입은 stack에서 값 그 자체로 갖고있어 외부로 데이터를 전달할때, **값을 일정한 형식의 raw byte 형태로 변경하여 전달할 수 있다.**

하지만, 위 그림과 같이 객체의 경우 실제로 Heap 영역에 존재하고 스택에서는 **Heap 영역에 존재하는 객체의 주소(메모리 주소)를 갖고 있다.**

<br>

❓ 이 주소 값을 그대로 다른 곳에 보낸다고 생각해보자.

먼저 프로그램이 종료되거나 객체가 쓸모없다고 판단되면 Heap 영역에 있던 데이터는 제거된다. 따라서 본인 메모리에서도 데이터가 사라진다.

외부로 전송했다고 생각했을때에도, 전송받은 기기의 전달받은 메모리 주소에 내가 전송하려고 했던 데이터가 있을리가 없다.

<br>

따라서 이 주소값의 데이터(실체)를 Primitive 한 값 형식 데이터로 변환하는 작업을 거친 후, 전달해야한다.

그렇게 해야, 파일 저장이나 네트워크 전송시 파싱 할 수 있는 유의미한 데이터가 된다.

<br>

## 자바에서 직렬화를 하는 방법

<br>

### Serializable 인터페이스 구현

```java
class Sample implements Serializable {
}

class Sample2 extends Sample {
}
```

직렬화가 가능한 클래스를 만들기 위해서는 Serializable 인터페이스를 구현하도록 하면 된다.

혹은 Serializable 인터페이스를 구현한 클래스를 상속받으면 된다.



```java
class Sample implements Serializable {
     transient String name;
}
```

특정 필드를 직렬화하고 싶지 않은 경우에는 `transient` 키워드를 붙이면, 그 타입의 기본값(int인 경우 0, 객체인 경우 null)으로 직렬화된다.

만약, 직렬화 할 수 없는(Serializable 을 구현하지 않는) 객체를 필드 맴버로서  갖고 있다면, `java.io.InvalidClassException` 예외가 발생하여 직렬화 할 수 없다.



<br>

## 자바 직렬화 직접 해보기

```java
import java.io.*;
import java.util.Base64;

class Test {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        Person person = new Person("김철수", 19);
        
        byte[] serializedPerson;
        
        try (ByteArrayOutputStream baos = new ByteArrayOutputStream()) {
            try (ObjectOutputStream oos = new ObjectOutputStream(baos)) {
                oos.writeObject(person);
                serializedPerson = baos.toByteArray();
            }
        }
        // 바이트 배열로 생성된 직렬화 데이터를 base64로 변환
        System.out.println(Base64.getEncoder().encodeToString(serializedPerson));

        try (ByteArrayInputStream bais = new ByteArrayInputStream(serializedPerson)) {
            try (ObjectInputStream ois = new ObjectInputStream(bais)) {
                Object objectPerson = ois.readObject();
                Person newPerson = (Person) objectPerson;
                System.out.println(newPerson);
            }
        }
    }
}
class Person implements Serializable {
    String name;
    int age;
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

위와 같이 `name = "김철수" age = 19`인 `Person `객체를 ***OutputStream***클래스로 직렬화하고

직렬화한 객체를 다시 ***InputStream***클래스로 역직렬화하였다.

```
//결과
rO0ABXNyAAt0ZXN0LlBlcnNvbltDhYlZmYXYAgACSQADYWdlTAAEbmFtZXQAEkxqYXZhL2xhbmcvU3RyaW5nO3hwAAAAE3QACeq5gOyyoOyImA==
Person{name='김철수', age=19}
```



실제로 생성된 byte 배열을 출력해보면 (`Arrays.toString()` 사용)

```
[-84, -19, 0, 5, 115, 114, 0, 11, 116, 101, 115, 116, 46, 80, 101, 114, 115, 111, 110, 91, 67, -123, -119, 89, -103, -123, -40, 2, 0, 2, 73, 0, 3, 97, 103, 101, 76, 0, 4, 110, 97, 109, 101, 116, 0, 18, 76, 106, 97, 118, 97, 47, 108, 97, 110, 103, 47, 83, 116, 114, 105, 110, 103, 59, 120, 112, 0, 0, 0, 19, 116, 0, 9, -22, -71, -128, -20, -78, -96, -20, -120, -104]
```

엄청나게 긴 배열이 나온다.

타입에 대한 정보 등 클래스의 메타 정보도 가지고 있기 때문에 Json 같이 최소의 메타정보만 갖는 포맷보다 데이터가 많아진다.

💡 참고로, 직렬화 시킨 결과물이 json 형태로 직렬화 한것과 비교했을 때, 훨씬 용량이 크다는 점이 자바 직렬화의 단점 중 하나이다.

<br>

조금 무식해보일 수 있겠지만 한번 이 byte 배열을 그대로 넣고 역직렬화시켜보겠다.

직렬화가 잘 된 데이터라면, 역직렬화도 잘 되어야 할 것이다.

```java
class Test {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        Person person = new Person("김철수", 19);

        byte[] serializedPerson =
                {-84, -19, 0, 5, 115, 114, 0, 11, 116, 101, 115, 116, 46, 80, 101, 114, 115, 111, 110, 91, 67, -123,
                        -119, 89, -103, -123, -40, 2, 0, 2, 73, 0, 3, 97, 103, 101, 76, 0, 4, 110, 97, 109, 101, 116,
                        0, 18, 76, 106, 97, 118, 97, 47, 108, 97, 110, 103, 47, 83, 116, 114, 105, 110, 103, 59, 120, 112,
                        0, 0, 0, 19, 116, 0, 9, -22, -71, -128, -20, -78, -96, -20, -120, -104};

        try (ByteArrayInputStream bais = new ByteArrayInputStream(serializedPerson)) {
            try (ObjectInputStream ois = new ObjectInputStream(bais)) {
                Object objectMember = ois.readObject();
                Person newPerson = (Person) objectMember;
                System.out.println(newPerson);
            }
        }
    }
}
```

위 코드를 실행시키면

```
Person{name='김철수', age=19}
```

역직렬화가 잘 된다!

이로써 언젠가 사라져버리는 인스턴스 상태에서 영속화가 되었다!

<br>


## 직렬화 주의점

### 객체 맴버가 추가되거나 타입이 변경되었을 때

그러면, 만약 직렬화시킨 byte 배열을 그대로 갖고 있는 상태에서

Person 클래스 맴버 변수의 타입이 변경되거나 이름이 변경되거나 새로운 맴버 변수가 추가되면 어떻게 될까?

```
local class incompatible: stream classdesc serialVersionUID = 6576246705851303384, local class serialVersionUID = 2808296220477750099
```

테스트 해보면 알겠지만, 역직렬화 과정에서 위와 같은 에러가 발생한다.



에러 메세지를 확인해보면, `serialVersionUID`이 일치하지 않는다는 이야기인데, `serialVersionUID` 를 따로 명시해주지 않으면 클래스의 기본 해쉬값을 사용하게된다.

따라서, 우리가 Person 클래스에 변화를 주었으니 `serialVersionUID`도 새로운 값으로 변경된 것이고 위와 같은 에러가 발생한 것이다.

<br>

```java
class Person implements Serializable {
    private static final long serialVersionUID = 1L;
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

위와 같이 `serialVersionUID`를 명시해준 뒤, 직렬화를 다시 한다음에 `Person`에 새로운 맴버 변수를 추가해서 다시 역직렬화를 시도하면

에러가 발생하지 않음을 확인할 수 있다.

기존에 있던 맴버 변수를 삭제해도 에러가 발생하지 않는다.

<br>

⚠️ 단! 새로운 필드가 추가되는 것은 무시하는것으로 에러를 일으키지 않지만, 기존에 있던 `age`가 `int `타입에서 `long `타입으로 변경되면

```
Exception in thread "main" java.io.InvalidClassException: test.Person; incompatible types for field age
```

다시 에러를 일으킨다.


<br>


🌟 자바에서 제공하는 직렬화 기능은 ***추가적인 라이브러리 설치 없이 객체 데이터를 영속화시킬 수 있다는 장점***이 있지만,

직렬화 결과물 ***용량이 상대적으로 커서 비효율 적인 문제***를 갖고 있고,

자주 변경될 수 있는 데이터를 직렬화해서 보관하게 되면 **나중에 변경이 생겼을 때 역직렬화가 불가능해지므로 쓸모없는 데이터가 될 수 있다는 단점이 있다.**




<br>

{: .highlight-title }
> 참고한 글
>
> 1. [https://okky.kr/questions/224715](https://okky.kr/questions/224715)
> 2. [https://techblog.woowahan.com/2551/](https://techblog.woowahan.com/2551/)










