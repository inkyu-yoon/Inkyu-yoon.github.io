---
layout: post
title: "· 유클리드 호제법 (최대공약수·최소공배수)"
nav_order: 2
parent : Algorithm
grand_parent: 📚Learned
permalink: docs/Learned/Algorithm/FindGcd
---

# 유클리드 호제법 (최대공약수·최소공배수)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## 유클리드 호제법으로 최대공약수 구하기

<br>

- 유클리드 알고리즘은 **2개의 자연수의 최대공약수(GCD : Greatest Common Divisor)**를 구하는 알고리즘이다.
- 비교대상인 두 개의 자연수 a와 b에서 **(a>b)** a를 b로 나눈 나머지를 r이라고 했을 때, `GCD(a,b)=GCD(b,r)` 이고
  **r이 0이면, 그때 b가 a와 b의 최대 공약수 이다.**



```java
int gcd(int a, int b){//a>b라는 가정 하에
    if(a%b==0){
        return b;
    }
    return gcd(b,a%b);
}
```



위와 같은 재귀함수로 최대공약수를 구할 수 있다.

---



## 최소공배수() 구하기

<br>

두 자연수 `A×B=GCD×LCM` 성질을 이용한다. (LCM:Least Common Multiple)



위에서 구한 최대공약수를 이용하여 `LCM = A×B/GCD` 연산을 수행하면 된다.



