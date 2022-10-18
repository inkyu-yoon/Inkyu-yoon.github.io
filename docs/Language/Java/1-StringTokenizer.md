---
layout: default
title: "StringTokenizer 이해하기"
nav_order: 1
parent : Java
grand_parent: Language
permalink: docs/Language/Java/StringTokenizer
---

# 문자열을 원하는 구분자로 나누어 주는 StringTokenizer
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
   {:toc}

---


## StringTokenizer란?

- 문자열을 토큰 단위로 구분해서 만들어주는 클래스

```
StringTokenizer(String str ,String delim, boolean returnDelims)
```

1. String str 자리에는 문자열이 들어간다.
2. String delim(구분자) 자리에는 문자열을 어떤 문자를 기준으로 나누고 싶은지 넣으면 된다. 따로 입력하지 않으면 공백을 기준으로 문자열을 나눈다.
3. boolean returnDelims는 true를 입력하면 delim도 문자열에 인자로서 포함이 된다.
4. 분리된 문자열의 각 인자는 Token화가 되었다고 하고 토큰(분리된 문자열)들은 nextToken() 메서드를 통해 출력할 수 있다. (3개가 분리되었다면 Token이 3개가 있으므로 nextToken()을 세번 쓸 수 있다.)

<br>

------

## 예시를 통해 알아보기

예를 들어 "I love you" 라는 문자열이 들어있다고 가정해보자. (※ StringTokenizer 클래스를 import 해주어야 사용가능하다.)

![img](https://blog.kakaocdn.net/dn/p2wCT/btrJEKNwCNg/lETOkyFRpksVZ9y7X7iSlk/img.png)

구분자(delim)을 따로 입력하지 않았으니 default 값이 되므로

"I" "love" "you" 3개의 문자열(토큰)로 나누어짐을 알 수 있고,

Token이 3개 있으므로 nextToken()을 3번 사용해서 토큰들을 확인해보자.(토큰이 3개인데 nextToken()을 4번 사용하면 에러가 발생한다.)

![img](https://blog.kakaocdn.net/dn/bYW7i4/btrJIr0Nods/U8sTh52llvq0JvZq4WDOKk/img.png)

"I love you" 문자열 1개가 "I" "love" "you" 3개의 문자열로 나누어진 것을 확인할 수 있다.



![img](https://blog.kakaocdn.net/dn/bmlmMf/btrJLR5G7Ij/KvsKykKiUiGnpStFACroJ0/img.png)

문자열이 "I-love-you" 이므로 문자열에 공백이 따로 없기 때문에, delim을 "-"로 지정해 주어야

"I" "love" "you" 3개의 문자열로 나누어질 것이다.

![img](https://blog.kakaocdn.net/dn/blzeFX/btrJC5xTvtT/WdekP1VvAkuDVSWTk4fikk/img.png)



![img](https://blog.kakaocdn.net/dn/TIh0H/btrJEK7TKE5/4B63Ll5rCs8eTF6zCPdlg0/img.png)

StringTokenizer("I-love-you","-",true); 의 경우 returnDelims 에 true 값을 입력했기 때문에,

"I" "-" "love" "-" "you" 총 5개의 문자열로 나누어 질 것이라 예측할 수 있으므로 5번 println 해본다.

![img](https://blog.kakaocdn.net/dn/b0n28g/btrJKuiA4XK/FhUYvkvPklAMCqukyw9Xvk/img.png)

5개의 토큰으로 나누어졌다.



split() 메서드가 새로 생겨서, spilit() 메서드를 사용하는것이 편하긴 하다..ㅎ