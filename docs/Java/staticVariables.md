---
layout: default
title: "클래스와 인스턴스 변수·클래스 변수·지역변수"
nav_order: 1
parent : Java
---

## class(클래스)가 왜 필요한가?


-   객체를 생성하기 위해 필요하다.

---

## 객체가 왜 필요한가?



-   객체가 가진 속성과 기능을 사용하기 위해 필요하다.

---

## public 이 붙는 class 란?



-   하나의 소스 파일에 여러 클래스를 작성할 수 있다. 하지만, 클래스 중에 `public class` 의 이름은 소스파일의 이름과 일치해야 한다.

예를 들어 소스파일 이름이 `Helloworld.java` 이고 이 소스 파일 안에 `Hello` 라는 class 와 `Helloworld` 라는 class 가 있다고 할 때

```yaml
public class Hello {

}

class Helloworld {

}
// 소스파일과 이름이 다른 class 앞에 public 이 붙음.
```

위 상황은 에러가 발생하므로

```yaml
class Hello {

}

public class Helloworld {

}
```

소스 파일 명을 `Hello` 로 바꾸던지, `Helloworld` class 앞에 `public`을 붙여야 한다.

---

## class 의 변수



1.  **인스턴스 변수 (instance 변수)** : 클래스 영역에 선언되어 메인 클래스에서 객체를 생성할 때 생성되는 데이터
2.  **클래스 변수 (static 변수)** : 인스턴스 변수와 다르게 한 클래스에서 모두 공통된 값을 갖고, 객체 생성과 상관 없이 인스턴스 변수의 저장공간과 별도로 저장되어 있다.
3.  **지역 변수** : class 내의 메서드에서만 사용, 메서드가 종료되면 소멸된다.

```yaml
class Tv {
        static int width = 100;    //클래스 변수
        static int height = 50; // 클래스 변수
        int volume =1; // 인스턴스 변수

        int volume2Up(int volume){
            int a = 2;         //메서드에서만 사용되는 변수
            return volume + a;
        }
}

class Main {
    public static void main(String[] args){

        System.out.println(Tv.width); // static 변수는 객체 생성 없이 사용 가능
        System.out.println(Tv.height); // static 변수는 객체 생성 없이 사용 가능
        System.out.println(Tv.volume); // static 변수가 아닌 인스턴스 변수는 객체를 생성 후 사용 가능

        Tv t = new Tv();
        System.out.println(t.volume); // 객체를 생성하고 나서 사용하면 사용됨!
    }

}
```

위 예문을 보면 static이 붙은 클래스 변수는 객체 생성 없이 바로 사용해도 문제가 없지만, volume의 경우 static 변수가 아니기 때문에, t라는 객체를 생성한 후 객체를 이용해야 사용이 가능하다.

또한, static 변수는 생성한 객체로 변경이 가능하지만, 한번 변경하면 이 변수를 사용하는 모든 곳에 영향을 준다.

```yaml
class Tv {
        static int width = 100;    //클래스 변수
        static int height = 50; // 클래스 변수
        int volume =1; // 인스턴스 변수
}

class Main {
    public static void main(String[] args){

        Tv t = new Tv();
        t.width = 300; // 새로 생성한 객체로 static 변수를 변경
        t.volume = 2;
        System.out.println(t.width); // 변경된 300이 나올 것
        System.out.println(t.volume); // 변경된 2가 나올 것

        Tv t1 = new Tv();
        System.out.println(t1.width); // static 변수는 같은 메모리에서 가져오기 때문에 변경된 300이 나올 것
        System.out.println(t1.volume); // 인스턴스 변수는 객체별로 다른 메모리에 생성되므로 변경되지 않은 1이 나올 것
    }

}
```

t를 이용해서 static변수와 volume 값을 변경했다. static변수인 width는 다른 객체인 t2로 불러왔을 때 변화된 값이 나왔다.(공통된 값을 사용하므로) 

반면에, 인스턴스 변수인 volume은 변경해도 객체마다 독립된 저장공간에 보관하기 때문에 영향을 받지 않았다.

static 변수는 생성한 객체로 수정해도 사용하는 모든 곳에 영향을 주기 때문에, 변경 시 조심해야하고

꼭 변경하고 싶다면 객체이름을 사용해서 변경하는 것 보다 `Tv.width = 300` 클래스 이름을 사용해서 변경하는 것이 직관적으로 이해하기도 쉽고 바람직하다.

---

## 정리



-   인스턴스 변수는 각 객체마다 독립된 저장공간을 갖고 `class명 객체이름 = new class명();` 으로 생성한 후에 객체를 이용해서 다룰 수 있다.

-   클래스 변수(static 변수) 는 한 저장공간에서 갖고 오기 때문에, 변경하면 클래스 변수를 사용하는 모든 객체가 영향을 받고, 클래스 변수는 인스턴스 변수와 다르게 객체 생성 없이 사용할 수 있다.