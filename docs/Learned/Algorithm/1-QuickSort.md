---
layout: post
title: "· Quick Sort(퀵 정렬)"
nav_order: 1
parent : Algorithm
grand_parent: 📚Learned
permalink: docs/Learned/Algorithm/QuickSort
---

# Quick Sort(퀵 정렬) 알고리즘
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



이미 정렬이 많이 되어있는 상태에서는, 시간복잡도가 커질 수 있지만, 일반적으로 가장 빠른 정렬알고리즘이다.



간단하게 설명하면, 기준 값을 하나 정하고, `start index` `0`에서부터 기준값보다 크거나 값을 발견하고, `end index` 끝에서부터 기준값보다 작거나 같은 값을 발견할 시, 두수의 위치를 교환하고, start 인덱스는 +1 end 인덱스는 -1을 하고,



`start index`와 `end index`가 만날때 까지 교환을 수행하고, 루프가 끝난 뒤 `start index`의 위치를 기준으로 공간을 분리하여 또 위와같은 루프를 반복하면 최종적으로 정렬된 배열이 나오게 된다.

---


## 예시

<br>





```
[5] 3 8 (9) 2 4 {7}
```


**( [ ] = 시작인덱스 ( ) = 기준값 { } = 끝 인덱스 )**



시작 인덱스 5, 기준값 9 끝 인덱스 7 인 배열이 있을 때

------

```
5 3 8 [(9)] 2 4 {7}
```




시작 인덱스는 기준값과 값이 같은 9에서 멈춤, 끝 인덱스는 기준값보다 작은 7에서 멈춤, 두 수를 교환



기준 값은 7로 바뀔 것이다.



------

```
5 3 8 (7) 2 {4} [9]
```


시작 인덱스는 7보다 그거나 같은 값을 발견하지 못해 결국 9까지 왔고, 끝 인덱스는 7보다 작은 4를 발견했지만, 이미 시작 인덱스와 끝 인덱스가 만나 교차한 뒤 발견했으므로 교환을 수행하지 않음



시작 인덱스의 위치를 기준으로 공간을 분리한다.



------

```
[5] 3 (8) 7 2 {4}  ||  9
```


9는 값이 1개이므로 정렬을 수행할 필요가 없다.



------

```
5 3 [(8)] 7 2 {4} ||  9
```


위 경우도 기준값 8보다 크거나 같은 값을 발견하지 못해 시작 인덱스가 8까지 도달했고 끝인덱스는 4가 기준값 8보다 작으므로 4를 가리키고 있다. 두 위치를 교환한다.



------

```
5 3 (4) [7] {2} 8  ||  9
```


시작 인덱스는 기준값 4보다 큰 7을 발견했고, 끝 인덱스는 기준값 4보다 작은 2를 발견했다. 또한 두 인덱스가 만나기 이전이므로 교환을 수행한다.



------

```
5 3 (4) {2} [7] 8  ||  9
```


두번째 루프가 지난뒤에는 위와 같은 형태가 되고, 시작 인덱스의 위치인 7을 기준으로 공간을 또 분리한다.



------

```
[5] (3) 4 {2}  ||  [(7)] {8}  ||  9
```


위와 같은 경우도, 지금까지 사용했던 루프 메커니즘을 적용하면



첫번째 공간의 경우 기준값 3보다 큰 5와 기준값 3보다 작은 2를 교환하고 나면, 시작인덱스와 끝 인덱스는 3으로 모이게 된다.



두번째 공간의 경우 역시, 기준값 7로 시작인덱스와 끝 인덱스가 모이게 된다.





------

```
2 {[(3)]} 4 5  ||  {[(7)]} 8  ||  9
```


최종 결과는 위와 같을 것이고, 정렬이 되어있음을 확인할 수 있다.



랜덤한 배열로, 한번 단계별로 그려보면 이해가 쉬울 것이다.


<br>
<br>

---



## 코드

<br>



```java
static void quickSort(int [] arr){
    quickSort(arr, 0, arr.length-1); //초기 start 와 end 는 0과 arr.length-1 이므로, 만든 메서드, 사실 없어도 되는 메서드이긴 하다.
}

static void quickSort(int [] arr, int start, int end){
    int dividePoint = getDividePoint(arr, start , end);
    if(start<dividePoint-1){ //start가 dividePoint-1 보다 작다는 의미는, 충분히 교환할 원소가 다 없어졌다는 뜻이므로, 정렬이 끝난 상태
        quickSort(arr,start,dividePoint-1);
    }
    if(end>dividePoint){
        quickSort(arr,dividePoint,end);
    }
}
```



먼저 실행되는 구문은 위와 같다.

`pivotPoint` 는 초기값은 배열의 중간 지점으로 설정할 것이고, 첫 루프가 끝나면, 이동이 끝난 `start index` 를 `pivotPoint` 로 설정할 것이기 때문에

조건문을 위와 같이 작성했다.

이제 `getPivotPoint` 를 구현해야한다.



```java
static int getDividePoint(int [] arr , int start , int end){
    int Pivot = arr[(start+end)/2]; //피벗포인트는 항상 구분되는 배열의 중간값으로
    while(start<=end){ //start 가 end보다 뒤에 있을 때만 교환 수행해야함
        while(arr[start]<Pivot){ //pivot의 앞쪽은 작은 경우는 인덱스 증가
            start++;
        }
        while(arr[end]>Pivot){
            end--;
        }
        if(start<=end){ //한번 더 체크
            swap(arr,start,end); // 교환 후 인덱스 이동을 유지시켜야함
            start++;
            end--;
        }
    }
    return start; //루프가 다 끝나고 나면, start는 end인덱스의 오른쪽으로 넘어간 상태가 되고, 이 start포인트를 divide(분리) 포인트로 제공
}
```



이제 `swap` 메서드만 구현하면 되는데, `swap` 메서드 구현은 너무 간단하니 생략하겠다.



## 완성 코드



<br>



```java
class QuickSort {
    static void quickSort(int [] arr){
        quickSort(arr, 0, arr.length-1); //초기 start 와 end 는 0과 arr.length-1 이므로, 만든 메서드, 사실 없어도 되는 메서드이긴 하다.
    }

    static void quickSort(int [] arr, int start, int end){
        int dividePoint = getDividePoint(arr, start , end);
        if(start<dividePoint-1){ //start가 dividePoint-1 보다 작다는 의미는, 충분히 교환할 원소가 다 없어졌다는 뜻이므로, 정렬이 끝난 상태
            quickSort(arr,start,dividePoint-1);
        }
        if(end>dividePoint){
            quickSort(arr,dividePoint,end);
        }
    }
    static int getDividePoint(int [] arr , int start , int end){
        int Pivot = arr[(start+end)/2]; //피벗포인트는 항상 구분되는 배열의 중간값으로
        while(start<=end){ //start 가 end보다 뒤에 있을 때만 교환 수행해야함
            while(arr[start]<Pivot){ //pivot의 앞쪽은 작은 경우는 인덱스 증가
                start++;
            }
            while(arr[end]>Pivot){
                end--;
            }
            if(start<=end){ //한번 더 체크
                swap(arr,start,end); // 교환 후 인덱스 이동을 유지시켜야함
                start++;
                end--;
            }
        }
        return start; //루프가 다 끝나고 나면, start는 end인덱스의 오른쪽으로 넘어간 상태가 되고, 이 start포인트를 divide(분리) 포인트로 제공
    }

    static void swap(int[] arr, int start, int end) {
        int tmp = arr[start];
        arr[start] = arr[end];
        arr[end]=tmp;
    }
}
```

<br>

시간 제한이 까다로운 정렬 문제의 경우 퀵 정렬을 적극 활용해봐야겠다.