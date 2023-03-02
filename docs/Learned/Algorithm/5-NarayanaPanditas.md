---
layout: post
title: "· 숫자 순열 사전식 다음 순서 찾기 & 모든 순열 조합 찾기 : 나라야나 판디트 알고리즘"
nav_order: 5
parent : Algorithm
grand_parent: 📚Learned
permalink: docs/Learned/Algorithm/NarayanaPanditas
---


# 숫자 순열 사전식 다음 순서 찾기 & 모든 순열 조합 찾기 : 나라야나 판디트 알고리즘

<br>

파이썬의 `from itertools import permutaions`를 사용하지 않고 모든 숫자 순열의 조합을 찾을 수 있는 알고리즘이다.



{: .note-title }
> 조건
>
> ⓐ a[k] < a[k+1] 을 만족하는 가장 큰 인덱스 k를 찾는다. (존재하지 않는다면, 사전식 순서의 마지막인 전체 내림차순 정렬되어 있는 경우이다.)
>
> ⓑ a[k] < a[i] 가 되는 가장 큰 i를 찾는다.
>
> ⓒ a[k] 와 a[i] 의 위치를 교환한다.
>
> ⓓ k 이후의 값을 오름차순 정렬한다.

<br>
위 조건에 맞춰 코드를 짜면 된다.

예시로 확인해보면,

<br>

| 2    | 3    | 6    | 5    | 4    | 1    |
| ---- | ---- | ---- | ---- | ---- | ---- |

위와 같이 있다고 할때,

ⓐ 조건에 해당하는 부분은, a[0]<a[1] 도 있고 a[1]<a[2] 도 있다.

가장 큰 인덱스가 조건이므로, a[1]<a[2] 이고 k = 1이 된다.



ⓑ a[1]=3 보다 큰 원소는 a[2], a[3], a[4] 가 있는데, 가장 큰 i를 찾는 것이므로 a[4] 가 된다.



ⓒ a[1]=3 과 a[4]=4 이 위치를 바꾸면

| 2    | 4    | 6    | 5    | 3    | 1    |
| ---- | ---- | ---- | ---- | ---- | ---- |

위와 같이 된다.

바꾸고, k = 1 이후의 값을 오름차순 정렬하면

<br>

| 2    | 4    | 1    | 3    | 5    | 6    |
| ---- | ---- | ---- | ---- | ---- | ---- |

위와 같이 되고, 2 3 6 5 4 1 다음 사전식 순서인 2 4 1 3 5 6 이 된다.

<br>

```python
def next(arr):
    k =-1
    target = -1
    for i in range(len(arr)-1):
        if arr[i] < arr[i+1]:
            k = i

    # 내림차순 정렬로 되어 있는 경우
    if k == -1:
        return [-1]

    for i in range(k,len(arr)):
        if arr[k] < arr[i]:
            target = i

    arr[k],arr[target] = arr[target],arr[k]

    arr = arr[:k+1]+sorted(arr[k+1:])

    return arr
```

파이썬 코드로는 위와 같이 표현할 수 있다.

<br>

{: .highlight}
출처: https://stevenpcurtis.medium.com/leetcode-31-next-permutation-narayana-panditas-algorithm-eb1dcb4686a7

 