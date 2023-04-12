---
layout: post
title: "·  JAVA Call By Value vs Call By Reference?"
nav_order: 26
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/CallByValue
---

#  JAVA Call By Value vs Call By Reference?
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



먼저, Call By Value와 Call By Reference의 정의를 살펴보자.

- Call By Value : ***인자로 받은 값을 복사하여 처리한다.*** 복사한 값을 사용하므로 원본 데이터에 영향을 주지 않는다.
- Call By Reference : ***인자로 받은 값의 주소를 참조하여 처리한다.*** 원본 데이터를 사용하므로 값에 영향을 준다.

<br>

정의로 살펴보면, 자바에서 원시 타입을 다룰 땐, **Call By Value**이고 객체를 다룰 땐 **Call By Reference** 인 것 같다.

💡 **하지만! 자바는 오직 Call By Value로만 동작한다고 한다.**

<br>

```java
public class Test {
    public static void main(String[] args) throws NoSuchAlgorithmException {

        Test test = new Test();

        Sample sample = new Sample(1);
        test.test(sample);
        System.out.println(sample.value);
    }

    void test(Sample sample) {
        sample = new Sample(2);
    }
    
    
    static class Sample{
        int value;
        public Sample(int value) {
            this.value = value;
        }
    }
}
```

위와 같은 코드로 살펴보자.

`test()` 메서드는 Sample 객체를 파라미터로 받아 value값이 2인 Sample로 할당하고 종료된다.



💫 이전에 살펴본, Call By Reference 정의에 의하면 객체가 참조하고 있는 주소값을 전달하기 때문에

***메서드 수행 후*** value의 값은 변경되야 할 것이다.

하지만, 결과는 value는 그대로 1인 것을 확인할 수 있다.

<br>

그림으로 과정을 살펴보면,

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230412191843021.png" alt="image-20230412191843021" style="zoom: 40%;" />
</p>

test 메서드에 sample이 참조하고 있던 주소값을 넘겨준다.

그러면 test 스택에는 Heap 영역에 존재하고 있던 Sample 객체를 바라보고 있는 sample을 갖게 된다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230412192028508.png" alt="image-20230412192028508" style="zoom:40%;" />
</p>

그리고 test 메서드의 `sample = new Sample(2);` 에 의해 새로운 객체를 바라보는 sample으로 바뀐다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230412192223959.png" alt="image-20230412192223959" style="zoom:40%;" />
</p>

그리고 test 메서드가 종료되면, test 스택에 있던 sample은 사라지고 Heap 영역에 value값이 2인 객체는 남아있게 되고, GC에 의해 정리 될 것이다.

즉, 파라미터로 참조 변수를 넘기면, 메서드는 주소값을 복사해서 그 주소값을 바라보는 새로운 지역 변수를 하나 더 만드는 셈인 것이다.

그래서 원래 존재하던 sample에는 영향을 주지 않는다.



즉, 자바는 Call By Value 방식이라고 볼 수 있겠다!



<br>

{: .highlight-title }
> 참고한 블로그
>
> 1. [https://deveric.tistory.com/92](https://deveric.tistory.com/92)










