---
layout: post
title: "· 프로젝트 경로 내 텍스트 파일 읽기"
nav_order: 11
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/FileReader
---

# 프로젝트 경로 내 텍스트 파일 읽기 (BufferedReader , FileReader)

<br>

BufferedReader 와 FileReader 패키지를 잘 사용하면, 텍스트 파일로부터 데이터를 가져오는 것은 어렵지 않다.



```
BufferedReader br = new BufferedReader(new FileReader("./파일명"));
// ↓ 예시
//BufferedReader br = new BufferedReader(new FileReader("./Test.txt"));
```





위와 같이 선언한 후, BufferedReader 의 read(), readLine() 등등 원하는 메서드를 사용해서 출력하면 된다.



참고로 "./파일명" 은 프로젝트 디렉토리 최 상단에 파일이 있을 경우 사용 가능하고



폴더 최 상단에 위치한 것이 아닌, 하위 폴더에 있는 경우, 경로를 추가해주어야 한다.



예제를 통해 이해해보겠다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018174239220.png" alt="image-20221018174239220" style="zoom:80%;" />
</p>



먼저 프로젝트의 가장 최 상단에 Hello World This is test text file. 이라는 문장이 9개 써있는 Test.txt 파일이 있다.



이 텍스트 파일의 한 문장만 출력하싶다면



BufferedReader 와 FileReader를 사용하여 BufferedReader 객체를 선언한 후, br.readLine() 한번만 사용하면 될 것이다.


{% capture some_var %}
```java
import java.io.*;

public class ReadDataFromTextFile {
    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new FileReader("./Test.txt"));
        System.out.println(br.readLine());
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018174255480.png" alt="image-20221018174255480" style="zoom:80%;" />
</p>

원하는 결과물이 나왔다.

------









만약, 2 문장을 출력하고 싶은 경우, 방법은 다양하나, 가장 간단한 방법으로 구현해보면,




{% capture some_var %}
```java
import java.io.*;

public class ReadDataFromTextFile {
    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new FileReader("./Test.txt"));

        System.out.println(br.readLine());
        System.out.println(br.readLine());
    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


println을 한번 더 해주면 된다.





------

만약, 몇 문단인지 알 수 없는 텍스트 파일을 알아서 작성된 데이터까지 출력하고 싶다면 while문을 사용하면 된다.


{% capture some_var %}
```java
import java.io.*;

public class ReadDataFromTextFile {
    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new FileReader("./Test.txt"));
        String str;
        while ((str =br.readLine()) != null) {
        System.out.println(str);
        }

    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


먼저 br.readLine()으로 한 라인을 입력받고, 그 값을 변수 str에 넣은 다음, null 값이 아니라면 출력하도록 설정하면



텍스트 파일의 끝 지점까지 출력될 것이다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018174316252.png" alt="image-20221018174316252" style="zoom:80%;" />
</p>


원하는 결과가 나왔다.





------



마지막으로, 다른 위치에 텍스트 파일이 있는 경우를 해보겠다.




<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221018174329528.png" alt="image-20221018174329528" style="zoom:80%;" />
</p>

Test.txt 파일이 Lecture 패키지 안에 있는 Test 패키지 안에 있는 Testtest 패키지 안에 있다.



이와 같은 경우에는 "./Test.txt" 를 경로로 입력하면, 해당 파일을 찾을 수 없어 오류가 발생한다.


{% capture some_var %}
```java
import java.io.*;

public class ReadDataFromTextFile {
    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new FileReader("Lecture/Test/Testest/Test.txt"));
        String str;
        while ((str =br.readLine()) != null) {
        System.out.println(str);
        }

    }
}
```
{% endcapture %}
{% assign some_var = some_var | markdownify %}
{% include fix_linenos.html code=some_var %}


위와 같이 상세 경로를 더 추가해주어야 텍스트 파일을 찾을 수 있다.