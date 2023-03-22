---
layout: post
title: "· JVM의 실행 엔진(Execution Engine) 구성 요소와 역할"
nav_order: 14
parent : Java
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/Java/ExecutionEngine
---

# JVM의 실행 엔진(Execution Engine) 구성 요소와 역할
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<p align="center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317133300201.png" alt="image-20230317133300201" style="zoom:80%;" />

</p>

우리가 `.java` 형식의 소스파일을 만들면

자바 컴파일러(`javac`)는 우리가 만든 소스파일을 목적파일인 `.class` 파일로 변환한다.

참고로, 자바 컴파일러는 바이너리 코드가 아닌 가상 머신이 이해할 수 있는 바이트 코드 목적파일로 변환한다.

> 목적파일 : 컴파일러가 생성한 파일로 기계어나 이에 준하는 이진 코드로 이루어져 있다.

그렇게 변환한 파일을 JVM의 클래스 로더는 로딩 → 링크 → 초기화 과정을 거친다.

불러온 목적 파일을 실행하기 위한 클래스 및 메서드를 검증한 뒤, JVM이 운영체제로 부터 일정 할당받은 RunTime Data Area의 메서드 영역에 올려놓는다.

그렇게 RunTime Data Area에 올라온 목적파일을 각 운영체제가 이해할 수 있는 기계어로 변환하는 작업을 하는 것이 **실행 엔진**(Execution Engine) 이다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317133406702.png" alt="image-20230317133406702" style="zoom:80%;" />
</p>


바이트 코드를 기계어로 번역하는 역할은 **인터프리터**(Interperter)와 **JIT 컴파일러**가 담당한다.

<br>

## 인터프리터

<br>

인터프리터는 런타임 중에 바이트 코드를 한 라인씩 읽고 기계어로 번역한다.

반복 호출되는 코드를 **매번 번역해야해서 속도가 느리다는** 단점이 있다.

<br>



## JIT (Just In Time) 컴파일러

<br>

**인터프리터의 속도 문제를 개선**하기 위해 사용하는 컴파일러이다.

**자주 실행되는 바이트 코드 영역**을 런타임 중에 기계어로 컴파일하여 사용한다.

컴파일 임계치를 만족하는 코드는 JIT 컴파일러에 의해 컴파일이 수행된다.

- method entry counter : JVM 내에 있는 메소드가 호출된 횟수

- back.edge loop counter : 메소드가 루프를 빠져나오기까지 회전한 횟수

컴파일 임계치가 일정 횟수에 도달한 코드는 컴파일 하기에 충분하다고 판단되어 큐에 들어가 대기하고, 컴파일 쓰레드에 의해 컴파일 된다.

컴파일된 코드는 캐싱되어 사용할 수 있기 때문에 속도를 향상시킬 수 있다.

캐싱된 코드는 Runtime Data Area의 **Native Method Stack에 저장**되어 인터프리터에 의해 번역할 필요 없이 바로 실행된다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317133705213.png" alt="image-20230317133705213" style="zoom:80%;" />
</p>

- JIT 컴파일러는 내부적으로 바이트 코드를 최적화처리를 위한 IR이라는 중간 코드로 표현한다.
- Optimizer는 IR 코드를 최적화한다.
- Code Generator은 최적화된 중간코드를 기계어로 번역한다.
- Profiler는 반복 호출되는 메서드를 찾는 역할을 담당한다.







## Garbage Collector 가비지 컬렉터

<br>

클래스로더가 로딩이 끝나면 클래스 타입의 객체를 생성해서 메모리의 **Heap 영역에 저장**한다고 했었다.



가비지 컬렉터는 자동으로 메모리를 관리해주는 기능 중 하나로,

**Heap 영역** 메모리 공간에 할당된 객체 중 거의 사용되지 않거나 오래된 객체의 메모리를 반환함으로써 메모리 사용의 효율을 증가시킨다.

개발자 입장에서는 메모리 누수에 신경쓰지 않아도 되므로 개발에만 집중할 수 있다는 장점이 있다.

> `stop the world`란? : GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것이다. GC를 실행하는 쓰레드를 제외한 나머지 쓰레드는 모두 작업을 멈추고 GC 작업이 완료되면 중단했던 작업을 다시 시작한다. young 영역을 정리하는 minor gc는 크게 성능에 영향을 주지 않지만, old 영역을 정리하는 major gc로 인한 stop the worl는 애플리케이션에 영향을 줄 수 있다.
>
> **GC 튜닝이란** 이 **stop the world 시간을 줄이는 것**이다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317140957569.png" alt="image-20230317140957569" style="zoom:80%;" />
</p>

객체들은 실질적으로 **Heap 영역**에 생성되고 **Method Area나 Stack Area에서는 Heap Area에 생성된 객체의 주소만 참조하는 형식**으로 구성된다.

위와 같이, GC를 하는 시점에서 2번 블럭처럼 참조되지 않는 객체가 발견되고 이러한 객체들을 삭제하는 것이다.

이렇듯 참조하는 것을 확인하는 과정을 **Mark** 그리고 unreachable한 객체를 지우는 것을 **Sweep** 이라고 한다.

즉, 위 과정을 **Mark and Sweep** 과정이라고 한다.

<br>



## 가비지 컬렉터 동작 원리

<br>

가비지 컬렉터는

1. 대부분의 객체는 금방 접근 불가능한 상태가 된다.
2. 오래된 객체에서 젊은 객체로의 참조는 아주 적게한다.

이 2가지 조건을 바탕으로 Young 영역과 Old 영역으로 나누었다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317133812292.png" alt="image-20230317133812292" style="zoom:80%;" />
</p>

새롭게 생성된 객체의 대부분은 `Young Generation` 이라 불리는 `Young 영역`에 생성된다. 객체가 생성되고 나서 참조되지 않는 이유 등으로 Young 영역에 있던 객체들이 사라지는데 이를 `Minor GC` 가 발생했다고 한다.

`Young 영역` 에서 살아 남은 객체는 `Old Generation` 이라 불리는 `Old 영역`에 생성된다. 이 영역에 있던 객체가 사라지는 것을 `Major Gc` 혹은 `Full Gc` 가 발생했다고 한다.

<br>

### Young 영역

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230321210513376.png" alt="image-20230321210513376"  />
</p>

새로 생성된 대부분의 객체는 Eden 영역에 위치한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317141732501.png" alt="image-20230317141732501" style="zoom:80%;" />
</p>

Eden 영역이 꽉차면 unreachable 한 객체를 삭제하는 GC가 한 번 발생한 후, 살아남은 객체는 두개의 Survivor 영역 중 하나로 이동된다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317142058177.png" alt="image-20230317142058177" style="zoom:80%;" />
</p>

한 Survivor 영역에 쌓다가 Survivor 영역이 꽉차게 되면, 쌓여있던 객체를 다른 Survivor로 옮기기 전에 Minor GC를 통해 제거할것은 제거하고 살아남은 객체를 옮긴다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317142209068.png" alt="image-20230317142209068" style="zoom: 50%;" />
</p>

이 과정을 계속 반복하다가 계속해서 살아남는 객체(임계값에 도달한 객체)는 Old 영역으로 이동한다.

위 과정을 보면 알겠지만, **Survivor 영역 중 하나는 반드시 비어있는 상태로 남아 있어야 한다.**

> 만약 old 영역에 있는 객체가 Young 영역에 있는 객체를 참조하고 있다면?
>
> <img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317134318255.png" alt="image-20230317134318255" style="zoom:80%;" />
>
> 참고로 Old 영역에 있는 객체가 Young 영역에 있는 객체를 참조하면 Old 영역에 있는 카드 테이블에 정보가 저장된다.
>
> 따라서, GC를 실행할 때 카드 테이블을 통해서 GC 대상인지 식별한다.




<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317135643598.png" alt="image-20230317135643598" style="zoom:80%;" />
</p>

참고로 메모리 할당을 할 때, **마지막으로 메모리가 할당된 객체를 통해서 다음 객체를 할당**하는 방식인  **bump-the-pointer** 기술로 신속하게 처리할 수 있다.

하지만, **멀티 스레드 환경에서는 이 방식이 Thread-safe 하지 않기 때문에 락(Lock)을 사용할 수 밖에 없고 성능이 매우 떨어질 수 있다.**

그래서 개선된 방식이 **TLAB(Thread Local Allocation Buffer) 방식**이다.

Thread마다 할당을 위한 주소 범위를 부여해서 그 범위 내에서는 아무런 동기화 작업 없이 할당하게 하는 것이다.

많은 수의 스레드가 영역을 할당받은 채 사용되지 않으면 메모리 단편화가 발생할 수 있으니, 스레드별 사이즈를 감소시키는 방식을 사용할 수 있다.

<br>

### Old 영역

<br>

old 영역에 객체가 계속 쌓이면 메모리가 부족해지고, 이때 Major Gc가 발생한다.

<br>



## GC 방식 종류와 특징

<br>

### Serial GC

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230318142754023.png" alt="image-20230318142754023" style="zoom:80%;" />
</p>

```
java -XX:+UseSerialGC -jar Application.java
```

Serial Collector를 명시적으로 지정하려면 위와 같이 하면 된다.

Serial GC는 Young 영역에서는 Mark and Sweep 과정으로 수행되며 Eden 영역에서 Survior 영역으로 이동시키고 계속해서 살아남는 객체를 Old 영역으로 이동시킨다.

Old 영역에서는 Compact 과정도 추가된다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230318141417543.png" alt="image-20230318141417543" style="zoom:80%;" />
</p>

객체를 정리한 뒤, 남은 객체들을 한 곳으로 모으는 작업이다.

Serial GC는 1개의 쓰레드만을 이용하기 때문에 CPU의 코어가 여러개인 운영 서버에서 Serial GC를 사용하는 것은 피해야한다.

<br>

### Parallel GC

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230318142830320.png" alt="image-20230318142830320" style="zoom: 67%;" />
</p>

```
java -XX:+UseParallelGC -jar Application.java

// 사용할 쓰레드의 갯수
-XX:ParallelGCThreads=<N>

// 최대 지연 시간
-XX:MaxGCPauseMillis=<N>
```



처리과정은 Serial GC와 비슷하며, 여러개의 스레드로 병렬적으로 처리하는 방식이다.

최대 지연 시간 또는 쓰레드의 개수를 설정할 수 있다.

GC 처리량을 증가시킬 수 있어 Serial 방식보다 Stop the world 가 짧다.

Java 8의 default GC 이다.

Java 9 이후는 G1 GC가 default GC 이다.

<br>

### Parallel Old GC

<br>

```
java -XX:+UseParallelOldGC -jar Application.java

// 사용할 쓰레드의 갯수
-XX:ParallelGCThreads=<N>
```



Parallel GC 방식에서 Old 영역 GC 과정을 Mark Sweep Compact가 아닌 **Mark Summary Compaction** 방식이 사용된다.

Parallel Old GC 방식은 Old 영역에서도 멀티 스레드 방식을 사용한다.

1. mark단계에서는 old영역을 region별로 나누고 region별로 살아있는 객체를 식별한다.
2. summary단계에서는 region별 통계정보로 살아있는 객체의 밀도가 높은 부분이 어디까지 인지 dense prefix를 정한다. 오랜 기간 참조된 객체는 앞으로 사용할 확률이 높다는 가정하에 dense prefix를 기준으로 compact영역을 줄인다.
3. compact단계에서는 compact영역을 destination과 source로 나누며 살아있는 객체는 destination으로 이동시키고 참조되지 않는 객체는 제거한다.

<br>

### CMS(Concurrent Mark Sweep) GC

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230321200931983.png" alt="image-20230321200931983" style="zoom:80%;" />
</p>

Parallel GC와 마찬가지로 여러 개의 쓰레드를 이용한다.

Initial Mark 단계에서 클래스 로더에 가장 가까운 객체 중 살아 있는 객체만 찾는다.

Concurrent Mark 단계에서는 stop the world 동안 마킹해놓은 객체가 참조하고 있는 객체들을 확인한다. 이 과정은 stop the world 없이 진행된다.

Remark 단계에서는 Concurrent Mark 단계동안 추가되거나 참조가 끊긴 객체를 확인한다.

Concurrent Sweep 단계에서는 쓰레기를 정리하는 작업을 실행한다. 이 작업도 stop the world 없이 진행된다.

다른 방식보다 메모리와 CPU를 더 많이 사용한다는 단점이 존재하고,

GC 과정에 Compaction 작업이 따로 없어서 나중에 Compaction 작업을 진행할 때 stop the world 시간이 길어질 수 있다.

<br>

### G1 GC

<br>

```
java -XX:+UseG1GC -jar Application.java

```

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230321201416422.png" alt="image-20230321201416422" style="zoom:80%;" />
</p>

G1 GC는 지금까지의 GC 방식과 메모리 구조가 완전히 다르다.

일정 크기의 논리적 단위인 region으로 구분하고 있다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230321202348236.png" alt="image-20230321202348236" style="zoom:80%;" />
</p>

1. **Initial Mark** : Old Region에 존재하는 객체들이 참조하는 Survivor Region을 찾는다. (stop the world 발생)
2. **Root Region Scan** : 위에서 찾은 Survivor 영역의 객체들에 대한 스캔 작업을 실시한다
3. **Concurrent Mark** : 전체 Heap의 scan 작업을 실시하고, GC 대상 객체가 발견되지 않은 Region은 이후 단계를 제외한다.
4. **Remark** : 애플리케이션을 멈추고(stop the world 발생) 최종적으로 GC 대상에서 제외할 객체를 식별한다.
5. **Cleanup** : 애플리케이션을 멈추고(stop the world 발생) 살아있는 객체가 가장 적은 Region에 대한 미사용 객체를 제거한다.
6. **Copy** : GC 대상의 Region이었지만, Cleanup 과정에서 완전히 비워지지 않은 Region의 살아남은 객체들을 새로운 Region(Available/Unused) Region에복사하여 **Compaction**을 수행한다. (stop the world 발생)
7. 살아있는 객체가 아주 적은 Old 영역에 대해 [GC pause(mixed)] 를 로그로 표시하고, Young GC가 이루어질 때 수집되도록 한다.

<br>

### Z GC

Z GC는 개발되고 있는 알고리즘이다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230321202951796.png" alt="image-20230321202951796" style="zoom:67%;" />
</p>

메모리를 재배치 하는 과정을 bit pointer라는 것을 활용하여 stop the world 과정 없이 진행한다고 한다.

1. Mark Start `STW` : ZGC의 Root에서 가리키는 객체 Mark 표시
2. Concurrent Mark/Remap: 객체의 참조를 탐색하면서 모든 객체에 Mark 표시
3. Mark End `STW` : 새롭게 들어온 객체들에 대해 Mark 표시
4. Concurrent Pereare for Relocate: 재배치하려는 영역을 찾아 Relocation Set에 배치
5. Relocate Start `STW` : 모든 Root 참조의 재배치를 진행하고 업데이트
6. Concurrent Relocate: 이후 **Load Barriers** 를 사용하여 모든 객체를 재배치 및 참조 수정


{: .highlight-title }
> 참고한 블로그
>
> 1. [https://velog.io/@impala/JAVA-JVM-Execution-Engine](https://velog.io/@impala/JAVA-JVM-Execution-Engine)
> 2. [https://mirinae312.github.io/develop/2018/06/04/jvm_gc.html](https://mirinae312.github.io/develop/2018/06/04/jvm_gc.html)
> 3. [https://mirinae312.github.io/develop/2018/06/04/jvm_memory.html](https://mirinae312.github.io/develop/2018/06/04/jvm_memory.html)
> 4. [https://d2.naver.com/helloworld/1329](https://d2.naver.com/helloworld/1329)
> 5. [https://inpa.tistory.com](https://inpa.tistory.com)
> 6. [https://mangkyu.tistory.com/119](https://mangkyu.tistory.com/119)
> 7. [https://steady-coding.tistory.com/590](https://steady-coding.tistory.com/590)
> 8. [https://huisam.tistory.com/entry/jvmgc](https://huisam.tistory.com/entry/jvmgc)


{: .highlight}
[【 JVM 메모리 구조 (RunTime Data Area) 알아보기 】](https://inkyu-yoon.github.io/docs/Language/Java/JVMRuntimeDataArea)
















