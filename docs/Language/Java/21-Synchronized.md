---
layout: post
title: "· JAVA 동기화와 Synchronized"
nav_order: 21
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/Synchronized
---

#  JAVA 동기화와 Synchronized
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>


{: .important-title}
> Synchronized 핵심!
> 
> 멀티 쓰레드 프로세스의 경우, 쓰레드가 같은 공유자원에 동시에 접근해서 작업을 하게되면 오류를 발생시킬 수 있다.
> 
> 따라서, 한 쓰레드가 공유 자원을 사용하고 있는 경우, 다른 쓰레드가 접근하지 못하도록 하는 것이 중요한다.
> 
> 이와 같은 방법으로, 데이터의 일관성과 균일성을 유지시키는 것을 동기화라고 하고, 
> 
> 자바에서는 Synchronized 키워드 혹은 Lock 클래스를 이용해 한 쓰레드만 자원을 사용할 수 있도록 제어한다.
> 
> Lock 클래스의 가장 대표적인 Reentrantlock은 배타락으로, 락 잠금과 해제를 명시할 수 있고, fair 파라미터를 통해 쓰레드 자원 할당 공정성을 설정할 수 있다는 점에서
> 
> 락 잠금과 해제가 자동적으로 이루어지고 대기중인 쓰레드의 순서를 보장하지 않는 Synchronized 방식과 차이점이 있다.

<br>

## 자바의 synchronized



멀티 쓰레드 프로세스의 경우, **여러 쓰레드가 같은 프로세스 내의 자원을 공유해서 작업**하기 때문에 서로의 작업에 영향을 주게된다.

이러한 일을 방지하기 위해서 한 쓰레드가 특정 작업을 끝마치기 전까지 다른 쓰레드에 의해 방해받지 않도록 하는 것이 **임계영역(critical section)과 잠금(lock)이다.**

공유 데이터를 사용하는 코드를 임계 영역으로 지정해놓고, 공유 데이터가 가지고 있는 **lock을 획득한 단 하나의 쓰레드만 이 영역 내의 코드를 수행**할 수 있게 한다.

해당 쓰레드가 임계 영역내의 모든 코드를 수행하고 나서 **lock을 반납해야만 다른 쓰레드가 lock을 받아 코드를 수행**할 수 있게 된다.

<br>

이처럼 **한 쓰레드가 진행 중인 작업을 다른 쓰레드가 간섭하지 못하도록 막는 것**을 '**쓰레드의 동기화(synchronization)**'라고 한다.



### synchronized를 이용한 동기화

```java
// 1. 메서드 전체를 임계 영역으로 지정

public synchronized void sample(){
//임계영역
}

```

메서드 전체가 임계 영역으로 설정된다.

쓰레드는 synchronized 메서드가 호출된 시점부터

해당 메서드가 포함된 ***객체의 lock***을 얻어 작업을 수행하다가 메서드가 종료되면 lock을 반환한다.


<br>



```java
// 2. 특정 영역을 임계 영역으로 지정

synchronized(객체의 참조변수) { // 락을 걸고자 하는 객체
//임계영역
}
```

쓰레드는 지정된 객체의 lock을 얻게 되고, 블럭을 벗어나면 lock을 반납한다.

<br>


💡***synchronized***는 참고로, 객체의 ***인스턴스에 Lock을 거는 것***이므로, 각 쓰레드가 다른 인스턴스에 접근하면 동시에 작업이 이루어진다.

<br>


```java
public class Test {
    public static void main(String[] args) {
        A a = new A();
        Thread thread1 = new Thread(() -> {a.run("thread1");});
        Thread thread2 = new Thread(() -> {a.run("thread2");});
        
        thread1.start();
        thread2.start();
    }
}

class A {
    public synchronized void run(String name) {
        System.out.println(name + " lock");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(name + " unlock");
    }
}

// 출력 결과
thread1 lock
thread2 lock
thread1 unlock
thread2 unlock
```

위와 같은 상황을 보면, A에는 run()이라는 synchronized 메서드가 존재한다.

```java
A a = new A();
Thread thread1 = new Thread(() -> {a.run("thread1");});
Thread thread2 = new Thread(() -> {a.run("thread2");});
```

그리고 메인 메서드에서, A 인스턴스를 생성 후, 쓰레드 두개가 동일한 인스턴스 a의 synchronized 메서드에 접근하게 되면

thread1이 먼저 lock을 획득하고 작업이 끝난 뒤, thread2가 lock을 획득해서 작업을 진행한다.

<br>

```java
A a = new A();
A a2 = new A();
Thread thread1 = new Thread(() -> {a.run("thread1");});
Thread thread2 = new Thread(() -> {a2.run("thread2");});
```

만약 위와 같이 다른 인스턴스에 접근하는 경우라면, 동시에 메서드가 실행되는 것을 확인할 수 있다.

<br>

```java
package test;

public class Test {
    public static void main(String[] args) {
        A a = new A();
        Thread thread1 = new Thread(() -> {a.run("thread1");});
        Thread thread2 = new Thread(() -> {a.print("thread2");});

        thread1.start();
        thread2.start();
    }
}

class A {
    public void print(String name){
        System.out.println(name + " hello");
    }
    public synchronized void run(String name) {
        System.out.println(name + " lock");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(name + " unlock");
    }
}

// 실행 결과
thread1 lock
thread2 hello
thread1 unlock
```

💡 참고로, 여러 쓰레드가 동일한 인스턴스에 접근한다고 해도, synchronized 메서드가 아닌 경우에는 동시에 접근이 가능하다.

같은 인스턴스 a에 쓰레드가 접근했지만, 한 쓰레드는 synchronized 메서드가 아닌 print()에 접근했고, 다른 쓰레드는 synchronized  메서드가 붙은 run() 메서드에 접근했다.

synchronized 가 적용되지 않은 print() 메서드는 대기 없이 바로 호출되는 것을 확인할 수 있다.

<br>

❓그렇다면 static synchronized 메서드는 어떻게 동작할까?

<br>


```java
package test;

public class Test {
    public static void main(String[] args) {
        A a = new A();
        A a2 = new A();
        Thread thread1 = new Thread(() -> {a.run("thread1");});
        Thread thread2 = new Thread(() -> {a2.run("thread2");});

        thread1.start();
        thread2.start();
    }
}

class A {
    public static synchronized void run(String name) {
        System.out.println(name + " lock");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(name + " unlock");
    }
}
// 실행 결과
thread1 lock
thread1 unlock
thread2 lock
thread2 unlock
```

static 메서드는 알다시피 인스턴스를 생성하지 않고도 사용할 수 있는메서드이다.

따라서, static synchronized 메서드는 각 쓰레드가 다른 인스턴스에 접근했지만, 순차적으로 진행됨을 확인할 수 있다.

<br>


### Class Lock과 Instance Lock

```java
package test;

public class Test {
    public static void main(String[] args) throws InterruptedException {
        A a = new A();
        A a2 = new A();
        Thread thread1 = new Thread(() -> {a.run("thread1");});
        Thread thread2 = new Thread(() -> {a2.run("thread2");});

        thread1.start();
        thread2.start();
    }
}

class A {
    public void run(String name) {
        synchronized (this){
            System.out.println(name + " lock");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(name + " unlock");
        }
    }
}

```

위 코드에서 각 쓰레드는 다른 인스턴스에 접근하고 있으므로 실행결과는 다음과 같다.

```
thread1 lock
thread2 lock
thread1 unlock
thread2 unlock
```

<br>


```java
class A {
    public void run(String name) {
        synchronized (A.class){
            System.out.println(name + " lock");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(name + " unlock");
        }
    }
}
```

하지만 위와 같이 `synchronized (A.class)` 해주게 되면 인스턴스 단위로 lock이 걸리는 것이 아닌, 클래스 단위로 lock이 걸린다.

따라서, 다른 인스턴스에 접근했다고 할지라도, 아래와 같이 순차적으로 실행되는 것을 확인할 수 있다.

```
thread1 lock
thread1 unlock
thread2 lock
thread2 unlock
```



<br>




## ReentrantLock 이란?

synchronized 를 사용하면, 자동적으로 lock이 잠기고 풀리기 때문에 편하지만, 같은 메서드 내에서만 lock을 걸 수 있고 lock 잠금과 해제를 명시할 수 없어 불편한 부분도 존재한다.

그럴때 사용하는 클래스가 `java.util.concurrent.locks` 패키지에 있는 lock 클래스 이고, ***ReentrantLock*** 는 그 중에 하나이다.

```java
 ReentrantLock()
 ReentrantLock(boolean fair)
```

위와 같이 2개의 생성자를 갖고 있고, fair가 true인 경우 lock이 풀렸을 때, 가장 오래 기다린 쓰레드가 lock을 획득할 수 있게 처리한다.

동기화 순서를 보장할 수 있다는 점에서 synchronized와 차이가 있다고 할 수 있다.

다만, 어떤 쓰레드가 가장 오래 기다렸는지 확인하는 과정을 거쳐야 하므로 성능은 떨어진다.

<br>


```java
void lock()
void unlock()
boolean isLocked()
```

위 메서드를 사용하여 명시적으로 락을 걸고, 해제해야한다.

<br>


```java
lock.lock();
try{
    //임계영역
} finally{
    lock.unlock();
}
```

임계 영역에서 예외가 발생하면 lock이 풀리지 않을 수 있으므로 try-finally문으로 감싸는 것이 일반적이다.

<br>


```java
private ReentrantLock lock = new ReentrantLock(); 
private Condition condition = lock.newCondition();

condition.await()
condition.signal()
```

위와 같이 Condition 객체와 await() 메서드로 락을 반납후 기다리도록 할 수 있고 signal()로 다시 락을 얻어 재진입할수도 있다.

> synchronized를 사용할 경우 wait() 와 notify()

<br>


{: .highlight-title }
> 참고한 블로그
>
> 1. [https://backtony.github.io/java/2022-05-04-java-50/#lock-condition](https://backtony.github.io/java/2022-05-04-java-50/#lock-condition)










