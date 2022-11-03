---
layout: post
title: "· 소수 구하기 (에라토스테네스의 체)"
nav_order: 3
parent : Algorithm
grand_parent: 📚Learned
permalink: docs/Learned/Algorithm/Primary
---

# 소수 구하기 (에라토스테네스의 체)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}



소수를 출력하는 방법에는 여러가지가 있다.



소수란, 1 을 제외한 다른 수로 나누어지지 않는 수이기 때문에, 어떤 특정한 N 값 미만의 모든수를 직접 나누어보고 나머지가 0이 되지 않을 때 소수로 정의하는 방법이 있다.



하지만 이 방법은 불필요한 나눗셈 연산이 많이 사용이 되기 때문에, **더 효율적인 방법을 사용하는 것**이 좋다.



------

## 1. 에라토스테네스의 접근

<br>

그 방법 중에 하나로 에라토스테네스의 체 라는 방법이 있는데, 2부터 자기 자신을 제외한 배수를 제외시키는 방법이다.



예를 들어, 1000이하의 소수를 구해야 한다면,



2를 제외한 2의 배수 4,6,8,10,12 ·······1000을 지우고



3을 제외한 3의 배수 6,9,12,15,18 ·······999을 지우고

·
·
·

그렇게 지워나가면 소수만 남게 된다는 원리이다.





------



## 2. N의 제곱근 이하의 정수만 고려

<br>

주어진 N이 소수라면, N의 제곱근보다 크지 않은 어떤 소수로도 나눠지지 않는다.



수가 수를 나누면 몫이 발생하게 되는데 몫과 나누는 수, 둘 중 하나는 반드시 N의 제곱근 이하이기 때문이다.



------

## 에라토스테네스의 체

<br>

2부터 N-1까지의 수 중에서 2의 배수를 제외하고, 3의 배수를 제외하고 ····· 제곱근N이하의 정수까지 나눠서 걸러지지 않고 남은 수들이 모두 소수가 된다.

```java

import java.util.*; //사용자에게 입력받기 위해 import 선언

public class PrimeNumber {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("1~N까지의 소수를 구해드립니다 N을 입력하세요.(3이상)");

        //사용자에게 N을 입력받음
        int N = scanner.nextInt();

        //boolean 배열을 선언, 배열은 index가 0부터 시작하므로 입력받은 N 보다 1 큰 size로 설정
        boolean[] prime = new boolean[N + 1];

        //boolean의 기본값은 false 이고 0과 1은 true로 미리 지정한다.
        prime[0] = true;
        prime[1] = true;

        for (int i = 2; i * i <= N; i++) { // N의 제곱근보다 작은 정수의 배수들만 지우면 됨.
            for (int j = i * i; j <= N; j += i) { //i*i로 시작해야 자기 자신은 제외할 수 있다.
                //i*i로 시작한 j는 i만큼 더하여 증가하기 때문에, j는 N보다 작은 i의 배수가 될 것.

                //boolean의 기본값은 false 이기 때문에 배수에 속하는 것들을 true로 지정
                prime[j] = true;

            }
        }

        //　∴ 배수들은 모두 true가 된 상태이고 남은 소수들은 false 상태가 된다.


        //false인 소수를 index를 이용해 추출
        for (int i = 0; i <= N; i++) {
            if (!prime[i]) {
                System.out.println(i);
            }

        }
    }
}
```

