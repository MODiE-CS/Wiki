# 자바 핵심 개념 정리 - 객체 생성부터 예외 처리, 캡슐화까지

자바를 공부하다 보면 "객체를 만들면 어떻게 동작하지?"라는 질문이 자연스럽게 떠오르게 됩니다. 단순히 new 키워드를 써서 객체를 만든다고는 하는데 그게 실제로 메모리 상에서 어떻게 처리되는 걸까? 그리고 그 객체는 언제, 어떻게 사라질까? 이 글에서는 전 포스팅에 이어서 자바의 객체 생성과 메모리 해제 부터 하여 배열과 컬렉션, 상속과 다형성, 예외 처리, 캡슐화와 패키지 구성 등 자바의 핵심 개념들을 정리해보겠습니다.

## 객체는 어디에, 어떻게 만들어질까?

자바 프로그램이 실행되면 JVM(Java Virtual Machine)이 등장합니다. JVM은 메모리를 다음과 같이 나누어집니다:

* **Heap**: new로 만든 객체들이 저장되는 공간.
* **Stack**: 메서드 호출 시 생성되는 지역 변수와 매개변수가 저장됨.
* **Method Area (MetaSpace)**: 클래스 정보, static 변수 등.
* **PC Register / Native Stack**: 쓰레드의 실행 흐름을 추적하는 구조.

예를 들어 다음과 같은 코드가 실행되면 다음과 같은 순서가 이루어집니다.

```java
Student s = new Student();
```

1. Student 클래스의 생성자가 호출된다.
2. JVM은 Heap에 Student 객체를 위한 공간을 할당한다.
3. 생성자 안의 초기화 코드가 실행된다.
4. Stack에는 참조변수 s가 생기고, 해당 객체를 가리킨다.

## 생성자와 초기화 블록

초기화 블록은 모든 생성자보다 먼저 실행되는 블록입니다.

```java
class Person {
    {
        System.out.println("초기화 블록 실행");
    }
    Person() {
        System.out.println("생성자 실행");
    }
}
```

하지만 실무에서는 명시적이고 명확한 생성자 사용이 더 권장된다고 합니다.

## 자바의 메모리 해제: Garbage Collection

자바는 개발자가 명시적으로 객체를 해제하지 않습니다. 대신, JVM 내부의 Garbage Collector (GC)가 메모리 누수를 방지하기 위해 더 이상 사용되지 않는 객체를 자동으로 제거해줍니다.
GC는 참조 기반으로 작동합니다. 어떤 객체도 더 이상 참조되지 않는 상태가 되면, 즉 도달할 수 없는 객체가 되면 GC의 대상이 됩니다.

예시)

```java
Student s = new Student();
s = null; // 이전 객체는 더 이상 참조되지 않음 → GC가 수거
```

자바의 메모리 해제는 자동화되어 있어 편리하지만 객체 참조 관리를 잘못하면 메모리 누수가 발생할 수 있으므로 주의가 필요합니다. 예를 들어 정적 컬렉션에 객체를 저장해놓고 제거하지 않거나 리스너를 등록만 해놓고 해제하지 않는 경우가 이에 해당할 수 있겠지요.

## 예외 처리

자바에서 예외는 프로그램 실행 중 발생할 수 있는 예외 상황을 처리하기 위한 구조입니다. 예외 처리는 프로그램의 비정상 종료를 막고 예측 가능한 흐름으로 유지하기 위한 장치입니다.

### try-catch-finally

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("0으로 나눌 수 없습니다");
} finally {
    System.out.println("항상 실행됩니다");
}
```

* **try**: 예외 발생 가능성이 있는 코드를 감싼다.
* **catch**: 예외가 발생했을 때 실행된다.
* **finally**: 예외 여부와 관계없이 항상 실행된다.

### 예외 처리 구조

자바에서는 프로그램 실행 중 발생할 수 있는 오류를 처리하기 위해 예외 처리 구조를 제공합니다.
예외는 크게 두 가지로 나뉘는데요.

#### 체크 예외(Checked Exception)

* 컴파일 타임에 예외 처리 여부를 반드시 확인하는 예외로, 명시적으로 try-catch로 처리하거나 throws로 선언해야 합니다.
* 예: `IOException`, `SQLException`

#### 언체크 예외(Unchecked Exception)

* 런타임 중 발생하는 예외로, 처리 여부가 강제되지 않지만 상황에 따라 적절한 처리가 필요합니다.
* 예: `NullPointerException`, `IllegalArgumentException`

### throw vs throws

자바에서는 thorw로 예외를 직접 던지거나 throws로 메서드 선언부에 예외 발생 가능성을 명시할 수 있습니다.

* `throw`는 실행문으로서 예외 객체를 발생시키는 데 사용됩니다.

```java
if (num < 0) {
    throw new IllegalArgumentException("음수 안됨!");
}
```

* `throws`는 메서드 선언부에서 해당 메서드가 특정 예외를 발생시킬 수 있음을 명시할 때 사용됩니다. 이는 주로 체크 예외에서 필수로 요구됩니다.

```java
public void readFile(String path) throws IOException {
    Files.readAllLines(Path.of(path));  // 예외 발생 가능
}
```

정리하자면 throw는 예외를 실제로 "던지는 코드"이며 throws는 "던질 수 있다는 선언"입니다.

## 반복문 종류

자바에는 세 가지 반복문이 있습니다. 각 반복문을 선택하는 기준은 아래와 같습니다.

* **for**: 반복 횟수가 명확할 때
* **while**: 조건만 만족하면 반복
* **do-while**: 최소 한 번은 무조건 실행

## 배열과 ArrayList

```java
int[] arr = new int[3];
ArrayList<String> list = new ArrayList<>();
```

* 배열은 크기가 고정되어 있고, 성능은 빠르지만 유연성이 떨어집니다.
* ArrayList는 내부적으로 배열을 사용하면서 동적으로 크기 조절 가능합니다.

성능이 정말 중요하고 요소의 개수가 정해저 있으면 배열을,
코드가 간단하고 유연하게 작성되어야 하고 요소의 개수가 수시로 변하면 ArrayList를 사용하는게 좋습니다.

## 상속과 다형성, 객체지향

자바에서 상속은 기존 클래스(부모 클래스)의 필드나 메서드를 자식 클래스가 물려받아 사용할 수 있게 해주는 기능입니다. 상속은 코드의 재사용성과 확장성을 높여줍니다.

예:

```java
class Animal {
    void eat() {
        System.out.println("먹는다");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("멍멍");
    }
}
```

자바에서는 클래스 간 단일 상속만 허용됩니다. 이는 상속 구조가 너무 복잡해지거나 충돌이 발생하는 다이아몬드 문제를 방지하기 위함입니다.

부모 타입으로 자식 객체를 참조하면, 실행 시점에는 자식 클래스의 메서드가 호출되는 동적 바인딩이 일어납니다.

```java
Animal animal = new Dog();
animal.eat(); // 부모 클래스의 메서드 사용
// animal.bark(); // 불가능
```

## 오버로딩과 오버라이딩

오버로딩은 같은 이름의 메서드를 매개변수의 수나 타입만 다르게 하여 여러 번 정의하는 기법입니다. 컴파일 시점에 어떤 메서드가 실행될지 결정되므로 '정적 바인딩'이라고도 불립니다.

```java
class Printer {
    void print(String s) {
        System.out.println(s);
    }
    void print(int i) {
        System.out.println(i);
    }
}
```

오버라이딩은 부모 클래스에서 정의한 메서드를 자식 클래스가 같은 이름, 같은 매개변수, 같은 리턴타입으로 다시 정의하는 것입니다. 실행 시점에 어떤 메서드가 실행될지 결정되며 다형성을 실현하는 핵심이 됩니다.

```java
class Animal {
    void sound() {
        System.out.println("동물 소리");
    }
}
class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("멍멍");
    }
}
```

| 항목     | 오버로딩     | 오버라이딩       |
| ------ | -------- | ----------- |
| 정의 위치  | 같은 클래스 내 | 상속 관계 클래스 간 |
| 메서드 이름 | 동일       | 동일          |
| 매개변수   | 다름       | 동일          |
| 반환 타입  | 상관없음     | 반드시 동일      |
| 바인딩 시점 | 컴파일 시점   | 런타임 시점      |

이 둘은 목적이 다릅니다. 오버로딩은 "입력에 따라 다양한 처리", 오버라이딩은 "자식 클래스에서의 특화된 동작"에 초점을 둔다고 생각하면 쉽게 이해됩니다.

## 인터페이스와 추상 클래스

자바에서는 다중 상속이 불가능하지만 인터페이스를 통해 다중 구현이 가능합니다. 인터페이스는 메서드의 시그니처만 정의하고 구현은 해당 인터페이스를 구현하는 클래스가 맡습니다.

```java
interface Flyable {
    void fly();
}

class Bird implements Flyable {
    public void fly() {
        System.out.println("날개로 날다");
    }
}

class Plane implements Flyable {
    public void fly() {
        System.out.println("엔진으로 날다");
    }
}
```

반면 추상 클래스는 공통 속성 + 공통 동작을 함께 정의할 수 있는 클래스입니다. 일부 메서드는 구현하고 일부는 추상 메서드로 남겨 자식 클래스가 반드시 구현하게 만들 수 있습니다.

```java
abstract class Animal {
    abstract void sound();
    void breathe() {
        System.out.println("숨 쉬기");
    }
}

class Cat extends Animal {
    void sound() {
        System.out.println("야옹");
    }
}
```

| 항목     | 인터페이스                   | 추상 클래스                |
| ------ | ----------------------- | --------------------- |
| 목적     | 동작 정의 (기능 중심)           | 공통 속성+동작 정의 (상태 + 동작) |
| 다중 구현  | 가능                      | 불가능                   |
| 생성자    | 없음                      | 있음                    |
| 필드     | public static final 상수만 | 인스턴스 필드 가능            |
| 메서드 구현 | 기본적으로 없음 (default 가능)   | 가능                    |

정리하자면 공통 기능을 물려주는 뼈대가 필요할 땐 추상 클래스, 여러 역할(행위)을 선언하고 각 클래스가 자유롭게 구현하도록 하려면 인터페이스를 사용하는 게 좋습니다.

## 접근 제어자와 캡슐화

자바에서 접근 제어자는 클래스, 메서드, 필드의 접근 범위를 명확히 설정하여 정보 은닉과 캡슐화를 지원합니다.

| 접근 제어자    | 접근 가능 범위                            |
| --------- | ----------------------------------- |
| public    | 어디서든 접근 가능                          |
| protected | 같은 패키지 내, 또는 다른 패키지의 자식 클래스에서 접근 가능 |
| (default) | 같은 패키지 내에서만 접근 가능                   |
| private   | 같은 클래스 내에서만 접근 가능                   |

캡슐화는 데이터 은닉을 통해 클래스 내부의 데이터를 보호하고 외부에서는 메서드를 통해 제한적으로 접근할 수 있게 하는 것을 말합니다.

```java
public class Account {
    private int balance;

    public int getBalance() { return balance; }

    public void deposit(int amount) {
        if (amount > 0) balance += amount;
    }
}
```

## 패키지 구조

패키지는 자바에서 클래스들을 논리적으로 묶어 구조화 및 모듈화를 지원하는 개념입니다. 이를 통해 클래스 이름 충돌을 방지하고, 기능별로 코드를 정리하여 유지보수성을 높일 수 있습니다.

```java
package com.modie.user;

public class UserService {
    // 사용자 관리 로직
}
```

패키지 이름은 실제 폴더 경로와 일치해야 하며 일반적으로 도메인을 역순으로 사용하여 고유성을 확보합니다.

### 패키지를 사용하는 이유

* 코드 분류: 관련된 기능끼리 그룹화하여 관리하기 쉽습니다.
* 클래스 이름 충돌 방지: 같은 이름의 클래스를 서로 다른 패키지에 둘 수 있습니다.
* 모듈화 지원: 독립적으로 개발 및 유지보수할 수 있도록 지원합니다.
* 가독성 향상: 코드의 구조와 역할을 쉽게 파악할 수 있습니다.
* 접근 제어 강화: default 접근 제어자를 활용하여 패키지 내부 접근을 제한할 수 있습니다.

### 패키지 구성 방식

패키지를 구성하는 두 가지 주요 방식은 다음과 같습니다.

#### 도메인(기능) 중심 패키지 구조

```
com.modie.user
 ├─ User.java
 ├─ UserController.java
 ├─ UserService.java
 └─ UserRepository.java
```

기능별로 모든 관련 파일을 함께 묶음.

#### 레이어(역할) 중심 패키지 구조

```
com.modie
 ├─ controller
 │  └─ UserController.java
 ├─ service
 │  └─ UserService.java
 ├─ repository
 │  └─ UserRepository.java
 └─ domain
    └─ User.java
```

역할별로 파일을 나누어 관리합니다. 최근 실무에서는 기능 중심 구조(Domain-Oriented Structure)를 많이 선호한다고 하네요.

## final과 static, 그리고 상수 정의

둘 다 변경 불가능하거나 공유되는 요소를 정의할 때 사용되며 특히 함께 쓰일 경우 상수를 정의하는데 사용됩니다.

### final

final은 변수, 메서드, 클래스에 사용할 수 있으며 각각의 의미는 다음과 같습니다:

* 변수: 한 번 값이 할당되면 다시 변경할 수 없음 (상수처럼 동작)
* 메서드: 해당 메서드를 자식 클래스에서 오버라이딩할 수 없음
* 클래스: 해당 클래스를 상속받을 수 없음

예를 들어 다음은 변경이 불가능한 final 변수입니다:

```java
final int MAX_USERS = 100;
```

이 변수는 한 번 초기화되면 더 이상 값이 변경될 수 없습니다. 만약 나중에 MAX\_USERS = 200;과 같이 값을 다시 대입하면 컴파일 에러가 발생합니다.

참조형 변수에서 final을 사용하면, 참조 자체는 고정되지만 내부 값은 수정할 수 있습니다:

```java
final List<String> names = new ArrayList<>();
names.add("루니");       // 가능
names = new ArrayList<>(); // 참조 변경은 불가
```

### static

static은 클래스 단위로 메모리에 올라가는 키워드입니다. 객체를 생성하지 않아도 클래스명으로 접근이 가능하며 모든 인스턴스가 공유하는 값입니다.

```java
class MathUtil {
    static int add(int a, int b) {
        return a + b;
    }
}

MathUtil.add(3, 5); // 객체 생성 없이 호출 가능
```

static 키워드는 주로 다음과 같은 경우에 사용됩니다:

* 공통된 값을 여러 인스턴스가 공유할 때
* 유틸리티 메서드를 인스턴스 없이 제공할 때

## 마무리

지금까지 자바의 객체 생성부터 static과 final까지 핵심 개념들을 하나씩 정리해봤습니다.
처음에는 단순히 문법을 외우는 것 같지만  개념들은 서로 연결되어있고 이는 프로젝트를 설계하거나 유지보수할 때 강력한 무기가 되어줍니다.
앞으로도 "왜 이 구조를 쓰는가?"를 고민하며 꾸준히 해보도록 하겠습니다.
