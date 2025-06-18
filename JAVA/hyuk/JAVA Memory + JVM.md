# 메모리 관리와 JVM

## JVM 구조

### JVM의 주요 구성 요소

```
┌─────────────────────────────────────────┐
│              Class Loader               │
├─────────────────────────────────────────┤
│                                         │
│           Runtime Data Area             │
│  ┌─────────────┬─────────────────────┐  │
│  │   Method    │      Heap           │  │
│  │   Area      │                     │  │
│  ├─────────────┼─────────────────────┤  │
│  │   PC Reg    │   Direct Memory     │  │
│  ├─────────────┼─────────────────────┤  │
│  │   Stack     │   Native Method     │  │
│  │   Area      │   Stack             │  │
│  └─────────────┴─────────────────────┘  │
├─────────────────────────────────────────┤
│            Execution Engine             │
│  ┌─────────────┬─────────────────────┐  │
│  │ Interpreter │   JIT Compiler      │  │
│  ├─────────────┼─────────────────────┤  │
│  │             │  Garbage Collector  │  │
│  └─────────────┴─────────────────────┘  │
├─────────────────────────────────────────┤
│        Native Method Interface          │
└─────────────────────────────────────────┘
```

### 클래스 로더 (Class Loader)

**클래스 로딩 과정**

1. **Loading**: 클래스 파일을 메모리로 로드
2. **Linking**: 검증, 준비, 해석 과정
    - Verification: 바이트코드 검증
    - Preparation: static 변수 메모리 할당
    - Resolution: 심볼릭 참조를 실제 참조로 변환
3. **Initialization**: static 변수 초기화, static 블록 실행

**클래스 로더 계층구조**

```
Bootstrap Class Loader (최상위)
    ↓
Extension Class Loader
    ↓
Application Class Loader (System Class Loader)
    ↓
User-Defined Class Loader
```

**클래스 로딩 시점 (Lazy Loading)**

```java
public class ClassLoadingExample {
    static {
        System.out.println("ClassLoadingExample 클래스 로딩됨");
    }
    
    static class InnerClass {
        static {
            System.out.println("InnerClass 로딩됨");
        }
    }
    
    static class StaticInnerClass {
        static {
            System.out.println("StaticInnerClass 로딩됨");
        }
    }
    
    public static void main(String[] args) {
        System.out.println("main 시작");
        
        // 이 시점에 InnerClass 로딩
        new InnerClass();
        
        // StaticInnerClass는 독립적으로 로딩 가능
        new StaticInnerClass();
    }
}
```

### 실행 엔진 (Execution Engine)

**인터프리터 vs JIT 컴파일러**

| 구분      | 인터프리터 (Interpreter) | JIT 컴파일러            |
| ------- | ------------------- | ------------------- |
| 실행 방식   | 바이트코드를 한 줄씩 해석 실행   | 바이트코드를 네이티브 코드로 컴파일 |
| 초기 실행속도 | 빠름                  | 느림 (컴파일 시간 필요)      |
| 반복 실행속도 | 느림                  | 매우 빠름               |
| 메모리 사용  | 적음                  | 많음 (컴파일된 코드 저장)     |
| 최적화     | 제한적                 | 적극적 최적화             |

```
💡 JAVA 실행 엔진의 동작 방식
1. 초기 실행 : 프로그램이 시작되면 인터프리터가 바이트 코드를 즉시 실행
2. 프로파일링 : JVM이 코드 실행 패턴을 모니터링
3. 핫스팟 감지 : 자주 실행되는 메서드나 루프를 식별 (보통 1_500 ~ 10_000회 실행 후)
4. JIT 컴파일 : 핫스팟으로 판단된 코드를 네이티브 코드로 컴파일
```


## JVM 메모리 영역

### 1. Method 영역 (Metaspace - Java 8+)

**저장 내용**

- 클래스 메타데이터
- 상수 풀 (Constant Pool)
- static 변수와 메서드
- 메서드 바이트코드

```java
public class MethodAreaExample {
    private static final String CONSTANT = "상수";
    private static int staticVar = 100;
    
    static {
        System.out.println("static 블록 실행");
        staticVar = 200;
    }
    
    public static void staticMethod() {
        System.out.println("Static method called");
    }
}
```

**특징**

- 프로그램 시작부터 종료까지 메모리에 상주
- 모든 스레드가 공유
- Java 8 이전에는 PermGen, 이후 Metaspace

### 2. Heap 영역

**구조 (Generational Heap)**

```
┌─────────────────────────────────────────────────────┐
│                    Heap                             │
├─────────────────────────────┬───────────────────────┤
│         Young Generation    │    Old Generation     │
├──────────┬──────────────────┼───────────────────────┤
│   Eden   │  Survivor Space  │                       │
│   Space  │   S0  │    S1    │                       │
└──────────┴───────┴──────────┴───────────────────────┘
```

**Young Generation**

- **Eden Space**: 새로 생성된 객체가 위치
- **Survivor Space (S0, S1)**: Eden에서 살아남은 객체들 (메모리 단편화를 위해 같은 역할의 S0, S1 두가지를 운영)

**Old Generation (Tenured Space)**

- Young Generation에서 여러 번 GC를 거쳐 살아남은 객체들
- 큰 객체들도 직접 Old Generation에 할당될 수 있음

```java
public class HeapExample {
    public static void main(String[] args) {
        // Eden 영역에 객체 생성
        for (int i = 0; i < 1000; i++) {
            String str = new String("Object " + i);
            // str은 메서드 종료 시 참조 해제
        }
        
        // 큰 배열은 Old Generation에 직접 할당될 수 있음
        int[] largeArray = new int[1000000];
        
        // 지속적으로 참조되는 객체는 Old Generation으로 이동
        List<String> persistentList = new ArrayList<>();
        for (int i = 0; i < 100; i++) {
            persistentList.add("Persistent " + i);
        }
    }
}
```

### 3. Stack 영역

**스레드별 독립적 영역**

- 지역변수
- 매개변수
- 메서드 호출 정보
- 리턴 주소

```java
public class StackExample {
    public static void main(String[] args) {
        method1(10);
    }
    
    static void method1(int a) {
        int localVar = 20;
        System.out.println("method1: " + a + ", " + localVar);
        method2(a + localVar);
    }
    
    static void method2(int b) {
        String localStr = "method2";
        System.out.println("method2: " + b + ", " + localStr);
        // method2 종료 시 localStr, b는 스택에서 제거
    }
}
```

**스택 프레임 구조**

```
┌─────────────────────┐  ← 스택 포인터
│   method2()         │
│  - b: 30            │
│  - localStr: "..."  │
├─────────────────────┤
│   method1()         │
│  - a: 10            │
│  - localVar: 20     │
├─────────────────────┤
│   main()            │
│  - args: String[]   │
└─────────────────────┘
```

**스택 오버플로우 예시**

```java
public class StackOverflowExample {
    public static void main(String[] args) {
        try {
            recursiveMethod(0);
        } catch (StackOverflowError e) {
            System.out.println("스택 오버플로우 발생!");
        }
    }
    
    static void recursiveMethod(int depth) {
        System.out.println("깊이: " + depth);
        recursiveMethod(depth + 1);  // 무한 재귀 호출
    }
}
```

### 4. PC (Program Counter) Register

- 현재 실행 중인 JVM 명령어 주소 저장
- 스레드별로 독립적으로 존재
- 네이티브 메서드 실행 시에는 undefined

### 5. Native Method Stack

- JNI(Java Native Interface)를 통해 호출되는 네이티브 메서드들의 스택
- C/C++ 등으로 작성된 네이티브 라이브러리 메서드 호출 시 사용

## 클래스 로딩과 초기화

### 클래스 초기화 시점

1. **클래스의 인스턴스 생성**
2. **클래스의 정적 메서드 호출**
3. **클래스의 정적 변수 값 할당 및 사용**
4. **리플렉션으로 클래스 접근**
5. **하위 클래스 초기화 시 상위 클래스 초기화**

```java
public class InitializationExample {
    static {
        System.out.println("InitializationExample static 블록");
    }
    
    public InitializationExample() {
        System.out.println("InitializationExample 생성자");
    }
    
    static class InnerClass {
        static {
            System.out.println("InnerClass static 블록");
        }
        
        static final String COMPILE_TIME_CONSTANT = "컴파일타임상수";
        static String runtimeVariable = "런타임변수";
        
        static {
            System.out.println("InnerClass static 블록 2");
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 1. 컴파일타임 상수 접근 ===");
        System.out.println(InnerClass.COMPILE_TIME_CONSTANT); // 클래스 로딩 안됨
        
        System.out.println("=== 2. 런타임 변수 접근 ===");
        System.out.println(InnerClass.runtimeVariable); // 클래스 로딩됨
        
        System.out.println("=== 3. 인스턴스 생성 ===");
        new InitializationExample();
    }
}
```

### 초기화 순서

**클래스 로딩 시**

1. 정적 변수 기본값으로 초기화
2. 정적 변수 명시적 초기화
3. 정적 초기화 블록 실행

**인스턴스 생성 시**

1. 인스턴스 변수 기본값으로 초기화
2. 인스턴스 변수 명시적 초기화
3. 인스턴스 초기화 블록 실행
4. 생성자 실행

```java
public class InitializationOrder {
    // 1. 정적 변수 명시적 초기화
    private static String staticVar = initStaticVar();
    
    // 3. 정적 블록
    static {
        System.out.println("3. static 블록");
        staticVar = "정적 블록에서 변경";
    }
    
    // 5. 인스턴스 변수 명시적 초기화
    private String instanceVar = initInstanceVar();
    
    // 6. 인스턴스 블록
    {
        System.out.println("6. 인스턴스 블록");
        instanceVar = "인스턴스 블록에서 변경";
    }
    
    // 7. 생성자
    public InitializationOrder() {
        System.out.println("7. 생성자");
        instanceVar = "생성자에서 변경";
    }
    
    private static String initStaticVar() {
        System.out.println("2. 정적 변수 초기화 메서드");
        return "정적 변수 초기값";
    }
    
    private String initInstanceVar() {
        System.out.println("5. 인스턴스 변수 초기화 메서드");
        return "인스턴스 변수 초기값";
    }
    
    public static void main(String[] args) {
        System.out.println("1. main 시작");
        System.out.println("4. 인스턴스 생성 전");
        new InitializationOrder();
        System.out.println("8. 인스턴스 생성 후");
    }
}
```

## Garbage Collection

### GC의 동작 원리

**Reachability (도달 가능성)**

- **Reachable**: GC Root에서 참조 체인을 통해 도달 가능한 객체
- **Unreachable**: 어떤 참조 체인으로도 도달할 수 없는 객체 (GC 대상)

**GC Root 종류**

- JVM 스택의 지역변수와 매개변수
- 메서드 영역의 정적 변수
- JNI에서 생성한 객체

```java
public class GCExample {
    private static List<Object> staticList = new ArrayList<>(); // GC Root
    
    public static void main(String[] args) {
        // 지역변수 - GC Root
        Object localObject = new Object();
        
        // 참조 체인 생성
        List<String> list1 = new ArrayList<>();
        list1.add("data");
        
        List<String> list2 = list1; // list1과 동일한 객체 참조
        
        list1 = null; // list1 참조 해제, 하지만 list2가 여전히 참조
        
        // 이제 list2 = null 하면 ArrayList 객체는 Unreachable
        list2 = null;
        
        // 순환 참조 예시
        Node node1 = new Node();
        Node node2 = new Node();
        node1.next = node2;
        node2.next = node1;
        
        node1 = null;
        node2 = null; // 순환 참조지만 GC Root에서 도달 불가능하므로 GC 대상
        
        System.gc(); // GC 실행 요청 (보장되지 않음)
    }
    
    static class Node {
        Node next;
    }
}
```

### GC 과정

**1. Mark 단계**

- GC Root에서 시작하여 참조 관계를 따라가며 도달 가능한 객체들을 마킹

**2. Sweep 단계**

- 마킹되지 않은 객체들을 메모리에서 제거

**3. Compact 단계** (선택적)

- 살아남은 객체들을 한쪽으로 모아 메모리 단편화 해결 (여기서 한쪽이 S0이고 다른 한쪽이 S1)

### GC 종류

**Minor GC (Young Generation)**

```java
public class MinorGCExample {
    public static void main(String[] args) {
        // Eden 영역에 많은 객체 생성
        for (int i = 0; i < 100000; i++) {
            String temp = new String("temporary object " + i);
            // temp는 루프가 끝나면 참조 해제됨
        }
        
        // 살아남는 객체들
        List<String> survivors = new ArrayList<>();
        for (int i = 0; i < 1000; i++) {
            survivors.add("survivor " + i);
        }
        
        // Eden 영역이 가득 차면 Minor GC 발생
        // survivors 리스트의 객체들은 Survivor Space로 이동
    }
}
```

**Major GC (Old Generation)**

- Old Generation이 가득 찰 때 발생
- Minor GC보다 시간이 오래 걸림
- Stop-The-World 시간이 길어질 수 있음

**Full GC**

- Young Generation과 Old Generation을 모두 정리
- 가장 오래 걸리고 성능에 큰 영향

### Stop-The-World

**Stop-The-World란?**

- GC 실행을 위해 JVM이 애플리케이션 실행을 일시 정지하는 것
- 모든 스레드가 작업을 멈춤
- GC 성능 튜닝의 주요 목표는 이 시간을 줄이는 것

### GC 알고리즘

**Serial GC**

- 단일 스레드로 GC 수행
- 작은 애플리케이션에 적합
- Stop-The-World 시간이 김

**Parallel GC**

- 여러 스레드로 GC 수행
- 멀티코어 환경에서 효율적
- Java 8의 기본 GC

**G1 GC (Garbage First)**

- 큰 힙 메모리에 적합
- 낮은 지연시간 목표
- Java 9의 기본 GC

**ZGC, Shenandoah**

- 매우 낮은 지연시간 목표
- 대용량 힙에서도 일정한 성능

### GC 튜닝 고려사항

**메모리 설정**

```bash
# 힙 메모리 크기 설정
-Xms2g -Xmx4g

# Young Generation 크기 설정
-XX:NewRatio=3  # Old:Young = 3:1
-XX:NewSize=1g -XX:MaxNewSize=1g

# GC 알고리즘 선택
-XX:+UseG1GC
-XX:+UseParallelGC
-XX:+UseZGC
```

**모니터링 옵션**

```bash
# GC 로그 출력
-XX:+PrintGC
-XX:+PrintGCDetails
-XX:+PrintGCTimeStamps

# GC 로그 파일 저장
-Xloggc:gc.log
```

**튜닝 지표**

- **Throughput**: 전체 시간 대비 애플리케이션 실행 시간
- **Latency**: Stop-The-World 시간
- **Memory Footprint**: 메모리 사용량

```java
public class GCTuningExample {
    public static void main(String[] args) {
        // 애플리케이션 성능 측정
        long startTime = System.currentTimeMillis();
        
        // 메모리 집약적 작업
        performMemoryIntensiveTask();
        
        long endTime = System.currentTimeMillis();
        
        // 메모리 사용량 확인
        Runtime runtime = Runtime.getRuntime();
        long totalMemory = runtime.totalMemory();
        long freeMemory = runtime.freeMemory();
        long usedMemory = totalMemory - freeMemory;
        
        System.out.println("실행 시간: " + (endTime - startTime) + "ms");
        System.out.println("사용 메모리: " + (usedMemory / 1024 / 1024) + "MB");
        System.out.println("전체 메모리: " + (totalMemory / 1024 / 1024) + "MB");
    }
    
    private static void performMemoryIntensiveTask() {
        List<byte[]> memoryBlocks = new ArrayList<>();
        
        for (int i = 0; i < 10000; i++) {
            // 1MB 블록 생성
            memoryBlocks.add(new byte[1024 * 1024]);
            
            // 일부 블록 해제 (가비지 생성)
            if (i % 100 == 0 && !memoryBlocks.isEmpty()) {
                memoryBlocks.remove(0);
            }
        }
    }
}
```

### 메모리 누수 패턴과 해결

**1. 컬렉션에 계속 추가만 하는 경우**

```java
// 문제가 있는 코드
public class MemoryLeakExample1 {
    private static List<String> cache = new ArrayList<>();
    
    public void addToCache(String data) {
        cache.add(data); // 계속 추가만 하고 제거하지 않음
    }
}

// 해결된 코드
public class FixedExample1 {
    private static final int MAX_CACHE_SIZE = 1000;
    private static List<String> cache = new ArrayList<>();
    
    public void addToCache(String data) {
        if (cache.size() >= MAX_CACHE_SIZE) {
            cache.remove(0); // LRU 방식으로 제거
        }
        cache.add(data);
    }
}
```

**2. 리스너 등록 후 해제하지 않는 경우**

```java
// 문제가 있는 코드
public class MemoryLeakExample2 {
    private List<EventListener> listeners = new ArrayList<>();
    
    public void addListener(EventListener listener) {
        listeners.add(listener);
        // 리스너를 제거하는 메서드가 없음
    }
}

// 해결된 코드
public class FixedExample2 {
    private List<EventListener> listeners = new ArrayList<>();
    
    public void addListener(EventListener listener) {
        listeners.add(listener);
    }
    
    public void removeListener(EventListener listener) {
        listeners.remove(listener);
    }
    
    public void cleanup() {
        listeners.clear();
    }
}
```

**3. ThreadLocal 사용 후 정리하지 않는 경우**

```java
// 문제가 있는 코드
public class MemoryLeakExample3 {
    private static ThreadLocal<String> threadLocal = new ThreadLocal<>();
    
    public void setUserContext(String user) {
        threadLocal.set(user);
        // threadLocal.remove()를 호출하지 않음
    }
}

// 해결된 코드
public class FixedExample3 {
    private static ThreadLocal<String> threadLocal = new ThreadLocal<>();
    
    public void setUserContext(String user) {
        threadLocal.set(user);
    }
    
    public void clearUserContext() {
        threadLocal.remove(); // 반드시 정리
    }
}
```