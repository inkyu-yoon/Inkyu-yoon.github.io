---
layout: default
title: "배열을 복사하는 3가지 방법"
nav_order: 8
parent : Java
grand_parent: Language
permalink: docs/Language/Java/CopyMethod
---

# 배열을 복사하는 3가지 방법 (arraycopy / copyOf / clone)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## System.arraycopy()



```
System.arraycopy(복사할 배열, 복사할 인덱스 시작점, 붙여넣을 배열, 붙여넣을 배열의 시작점, 복사할 갯수);
```



arraycopy의 경우 import 없이 사용할 수 있는 복사 방법이다.



복사할 배열의 시작점과 복사할 데이터의 갯수를 설정할 수 있기 때문에, 특정 부분의 데이터를 복사하기에 이점이 있다.

------

<br>

## Arrays.copyOf()



```
붙여넣을 배열 = Arrays.copyOf(복사할 배열, 복사할 갯수);
```



copyOf를 사용하기 위해서는 java.util.Arrays를 import 해주어야 사용가능하다.



copyOf의 경우 복사 시작 인덱스는 0으로 고정되어 있어, 특정 부분 데이터를 복사할 순 없다.



특정 인덱스를 추출하는 arraycopy와 비슷한 작업을 하고 싶은 경우



```
붙여넣을 배열 = Arrays.copyOfRange(복사할 배열, 복사할 인덱스의 시작점, 복사할 갯수)
```



위와 같이 copyOfRange를 사용하면 된다.

------

<br>

## clone()


```
붙여넣을 배열 = 복사할 배열.clone();
```



clone() 메서드의 경우 가장 간단하고 직관적으로 보이는 장점이 있다.



배열 전체를 복사할때는 간단하고 유용하지만, 특정 인덱스를 지정할 수 없는 점이 단점이라고 할 수 있겠다.

------

<br>

## 예제


{% capture some_var %}
```java
import java.util.Arrays;

public class binary {
    public static void main(String[] args) {
    int [] original = {1,2,3,4,5};
    System.out.println("original = " +Arrays.toString(original));

    System.out.println("===========================");

    int [] arraycopy = new int[original.length];
    System.arraycopy(original, 0, arraycopy, 0, original.length);
    System.out.println("arraycopy = " + Arrays.toString(arraycopy));

    int [] copyOf = Arrays.copyOf(original,original.length);
    System.out.println("copyOf = "+Arrays.toString(copyOf));

    int [] copyOfRange = Arrays.copyOfRange(original,0,original.length);
    System.out.println("copyOfRange = "+Arrays.toString(copyOfRange));

    int [] clone = original.clone();
    System.out.println("clone = "+Arrays.toString(clone));

    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

아래와 같은 결과물이 나온다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018172502392.png" alt="image-20221018172502392"  />
</p>

------

<br>

## 속도 비교




<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018172534091.png" alt="image-20221018172534091" style="zoom: 80%;" />
</p>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018172601914.png" alt="image-20221018172601914" style="zoom:80%;" />
</p>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018172629824.png" alt="image-20221018172629824" style="zoom:80%;" />
</p>


------

{: .note-title }
> 정리
> 
> StackOverflow에서 찾은 답변에 의하면, 복사해야할 length가 늘어날 수록, loop를 활용한 복사 방법과 나머지의 속도가 차이가 심하게 벌어지는 것을 확일할 수 있고, 나머지 3가지 방법은 거의 비슷한 속도로 보인다.
>
> 다만, length가 50000인 경우를 살펴보면, arraycopy의 경우 int자료형에서 이상하게 시간이 급격하게 늘어나있다..?
>
> 이유는 잘 모르겠지만, 특정 인덱스를 복사하고 싶은 경우, Arrays.copyOf()를 사용하고 전체를 복사하고 싶은 경우 clone()을 활용하면 속도와 직관적인 이해 모두 잡을 수 있을 것 같다.