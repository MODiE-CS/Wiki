### 프로세스 vs 스레드

**프로세스(Process)**  
- 독립적 메모리 공간을 가짐 (코드, 데이터, 힙, 스택)  
- 다른 프로세스와 메모리 공간을 공유하지 않음  
- 생성 및 전환 비용이 크며, 한 프로세스의 오류는 다른 프로세스에 영향을 주지 않음

**스레드(Thread)**  
- 같은 프로세스 내에서 코드, 데이터, 힙을 공유  
- 각 스레드는 독립적인 스택을 가짐  
- 전환 비용이 낮고, 하나의 스레드 오류가 전체 프로세스에 영향을 줄 수 있음

---

### 멀티스레딩 사용 이유

**장점**  
- 응답성 향상 (UI 스레드와 작업 스레드 분리)  
- 멀티코어 CPU 활용  
- 자원 공유 효율적  
- 작업 분리 가능

**단점**  
- 동기화와 데드락 등의 복잡성  
- 디버깅 난이도 증가  
- 잘못된 설계 시 성능 저하  
- 메모리 및 컨텍스트 스위칭 오버헤드

---

### 스레드 생명주기

**상태 전이**
```
NEW → RUNNABLE ⇄ BLOCKED
  ↓       ↓         ↓
  └──→ WAITING ←────┘
         ↓
    TIMED_WAITING
         ↓
    TERMINATED
```

**상태 설명**  
- NEW: 스레드 객체가 생성됨  
- RUNNABLE: 실행 가능 상태, 실제 실행 여부는 스케줄러에 따름  
- BLOCKED: 락이 걸려 접근 대기 중  
- WAITING: 다른 스레드의 작업 완료까지 무한 대기  
- TIMED_WAITING: 지정 시간 동안 대기  
- TERMINATED: 실행 종료

---

### 스레드 생성 방법

**Java에서 스레드란?**  
- Thread 클래스 또는 Runnable 인터페이스를 이용해 실행 단위를 정의하고 병렬 처리 가능

**Thread 상속 vs Runnable 구현**

| 구분 | Thread 상속 | Runnable 구현 |
|------|-------------|----------------|
| 다중 상속 | 불가 | 가능 |
| 재사용성 | 낮음 | 높음 |
| 결합도 | 높음 | 낮음 |
| 권장 | 특정 상황 | 일반적 권장 |

**start() vs run() 차이**  
- start(): 새로운 스레드에서 run() 실행  
- run(): 일반 메서드처럼 현재 스레드에서 실행됨

**예시**
```java
public class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Running: " + Thread.currentThread().getName());
    }
}
```

---

### 스레드 우선순위

- 범위: 1(MIN_PRIORITY) ~ 10(MAX_PRIORITY), 기본값은 5  
- 우선순위를 높인다고 항상 먼저 실행되는 것은 아님  
- 자바의 우선순위는 JVM이 OS의 스케줄러에 전달하지만, 실제 스케줄링은 OS에 의존함  
- 일반적으로 동일한 우선순위를 가진 스레드는 round-robin 방식으로 실행

---

### 안전한 스레드 종료

**stop() 금지 이유**  
- 즉시 종료되면서 공유 자원 정리 누락 및 일관성 붕괴 발생

**권장 종료 방법**

1. 종료 플래그 사용  
```java
volatile boolean running = true;
public void run() {
    while (running) {
        // 작업 수행
    }
}
```

2. 인터럽트 사용  
```java
public void run() {
    while (!Thread.currentThread().isInterrupted()) {
        // 작업 수행
    }
}
```

3. finally 블록에서 리소스 정리

---

### 동기화(synchronization)

**왜 필요한가?**  
- 여러 스레드가 동시에 공유 자원에 접근하면 경쟁 상태(Race Condition)가 발생해 데이터 일관성이 깨질 수 있음  
- 예: `count++`는 읽기 → 연산 → 저장의 3단계로 구성되어 원자성이 없음

**synchronized 키워드**  
- 공유 자원을 한 번에 하나의 스레드만 접근하도록 보장  
- 메서드 전체 혹은 특정 블록에 사용 가능

**메서드 vs 블록 동기화**

| 항목 | 메서드 동기화 | 블록 동기화 |
|------|----------------|------------------|
| 범위 | 전체 메서드 | 선택 영역 |
| 성능 | 낮음 | 높음 |
| 제어 | 단순 | 세밀하게 가능 |

**락 객체 종류**  
- 인스턴스 락: synchronized(this)  
- 클래스 락: synchronized(ClassName.class)  
- 특정 객체 락: synchronized(lockObj)

---

### 동기화 문제점

**데드락**  
- 두 스레드가 서로 자원을 점유한 채 상대의 자원을 기다리는 상태  
- 발생 조건: 상호 배제, 점유 대기, 비선점, 순환 대기

**예방 방법**  
- 락 획득 순서 고정  
- 타임아웃 사용  
- 락 최소화 설계

**성능 저하 원인**  
- 불필요한 동기화 범위  
- 락 경합 과다 → 작업 분리, 락 분할로 개선

---

### wait(), notify(), notifyAll()

**역할**  
- 스레드 간 협력을 위한 통신 메커니즘  
- 조건을 만족할 때까지 대기하고, 만족 시 스레드를 깨움

**조건**  
- synchronized 블록/메서드 내에서만 호출 가능  
- 락 보유 상태에서만 사용 가능

**동작 설명**  
- wait(): 락을 반납하고 대기  
- notify(): 대기 중 하나의 스레드를 깨움  
- notifyAll(): 모든 대기 스레드를 깨움

**wait() vs sleep()**

| 구분 | wait() | sleep() |
|------|--------|---------|
| 락 해제 | 예 | 아니오 |
| 사용 위치 | synchronized 내 | 어디서나 |
| 깨우는 방법 | notify/notifyAll/interrupt | 시간 경과 또는 interrupt |
| 클래스 | Object | Thread |

**예시**
```java
class Buffer {
    private int data;
    private boolean hasData = false;

    public synchronized void produce(int value) throws InterruptedException {
        while (hasData) wait();
        data = value;
        hasData = true;
        notify();
    }

    public synchronized int consume() throws InterruptedException {
        while (!hasData) wait();
        hasData = false;
        notify();
        return data;
    }
}
```

---

### volatile 키워드

**용도**  
- 변수의 변경을 다른 스레드가 즉시 인지하도록 보장  
- 메인 메모리에서 항상 읽고 씀

**특징**  
- 가시성 보장  
- 명령어 재정렬 방지  
- 원자성은 보장하지 않음

**사용 예시**  
- 종료 플래그  
- 상태 체크 변수  
- 싱글톤 DCL(Double-Checked Locking)

```java
private static volatile Singleton instance;
```

---

### java.util.concurrent 패키지

**ExecutorService**  
- 스레드 풀 관리  
- 종류: FixedThreadPool, CachedThreadPool, SingleThreadExecutor

**CountDownLatch**  
- 특정 개수만큼 await() 호출 차단  
```java
CountDownLatch latch = new CountDownLatch(3);
latch.await();
```

**CyclicBarrier**  
- 지정된 수의 스레드가 모두 도달할 때까지 대기, 재사용 가능

**기타 도구**  
- ReentrantLock: synchronized보다 유연한 락  
- Semaphore: 동시 접근 수 제한  
- ConcurrentHashMap: 스레드 안전한 Map
