# JAVA 기초 정리

## JAVA의 특징

### 장점
- **플랫폼 독립성**: JVM 위에서 동작하여 운영체제에 독립적
- **자동 메모리 관리**: Garbage Collector를 통한 자동 메모리 관리
- **객체지향**: 캡슐화, 상속, 다형성, 추상화 지원
- **강력한 타입 체크**: 컴파일 시점에서 타입 안정성 보장
- **멀티스레딩 지원**: 동시 처리 작업 가능

### 단점
- JVM 위에서 동작하기 때문에 실행 속도가 상대적으로 느림
- 다중 상속 불가 (Interface, Composition Pattern으로 해결)
- 메모리 사용량이 상대적으로 많음

### JAVA의 컴파일 과정

```
1. 개발자가 .java 파일 생성
2. javac 명령어로 바이트코드(.class) 생성
3. Class Loader가 JVM 메모리로 로드
4. 실행엔진이 OS에 맞는 기계어로 변환
```

```
💡 바이트 코드
- 플랫폼 독립성을 실현
- JIT 컴파일러를 통한 최적화
- 동적 로딩 가능
```

---

## JAVA Data Type

### 기본 데이터 타입 (Primitive Type)

| 타입      | 크기    | 범위                                                     | 초기값      |
| ------- | ----- | ------------------------------------------------------ | -------- |
| byte    | 1byte | -128 ~ 127                                             | 0        |
| short   | 2byte | -32,768 ~ 32,767                                       | 0        |
| int     | 4byte | -2,147,483,648 ~ 2,147,483,647                         | 0        |
| long    | 8byte | -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807 | 0L       |
| float   | 4byte | ±3.40282347E+38F                                       | 0.0f     |
| double  | 8byte | ±1.79769313486231570E+308                              | 0.0d     |
| char    | 2byte | 0 ~ 65,535                                             | '\u0000' |
| boolean | 1byte | true, false                                            | false    |

### 참조 데이터 타입 (Reference Type)

- 객체의 주소값을 저장
- null로 초기화 가능
- Heap 영역에 값 저장, Stack 영역에 변수명, 포인터 저장 (기본 데이터 타입은 포인터 없이 Stack에 실제 값 저장)
- 예: List, Class, Interface, Enum

### JAVA String

JAVA의 String은 **불변성**의 특징을 지닌다.

```
💡 String 객체가 불변인 이유
- 캐싱 기능에 의한 메모리 절약과 속도 향상
  -> String Pool에서 동일 문자열 재사용
- thread-safe
  -> 불변이기 때문에 여러 쓰레드에서 안전
- 보안 기능
  -> 참조 문자열 변경 불가 → 보안성 강화
```

```java
String str = "Hello";
str += " World";  // 새로운 String 객체 생성, 기존 객체는 GC 대상
```


**StringBuilder**

- 가변 문자열 클래스 (String은 불변)
- 문자열을 직접 수정하므로 **성능이 뛰어남**
- **싱글 스레드 환경**에 적합

```java
StringBuilder sb = new StringBuilder();
sb.append("Hello");
sb.append(" World");
String result = sb.toString();
```


**StringBuffer**

- `StringBuilder`와 동일한 기능 + **Thread-safe**
- 내부 메서드가 `synchronized`로 동기화 처리
- **멀티 스레드 환경**에 적합하나, 단일 환경에서는 성능 저하 발생

**String vs StringBuilder vs StringBuffer**

| 구분       | String   | StringBuilder | StringBuffer |
|------------|----------|---------------|---------------|
| 가변성      | 불변     | 가변          | 가변           |
| 동기화      | X        | X             | O              |
| 성능        | 느림     | 빠름          | 보통           |
| 사용 환경   | 일반     | 단일 스레드    | 멀티 스레드     |

---

## JAVA Class 구성 요소

### JAVA Package

**클래스를 논리적으로 묶는 네임스페이스**

- 네임 스페이스 관리
- 접근 제어
- 코드 조직화

```java
package com.company.project.util;

import java.util.List;         // 특정 클래스
import java.util.*;            // 패키지 전체
import static java.lang.Math.*; // static import
```

```
💡 패키지 네이밍 규칙
- 소문자
- 도메인 역순
- 계층 구조
- 자바 키워드 사용 금지
```
---

### `final`

**수정 불가능한 요소**를 선언할 때 사용하는 Java 키워드

- **변수**: 상수로 사용. 값 변경 불가
- **메서드**: 오버라이딩 불가
- **클래스**: 상속 불가

```java
final int CONSTANT = 100;
final List<String> list = new ArrayList<>();
list.add("item"); // 가능
// list = new ArrayList<>(); // 컴파일 에러
```

```java
class Parent {
    final void method() {
        // 오버라이딩 불가
    }
}

final class FinalClass {
    // 상속 불가
}
```

---

### static 키워드

**클래스 수준에서 메모리에 고정적으로 할당되는 구성 요소**를 정의하는 Java 키워드

**특징**
- 클래스 로딩 시 메모리 할당
- 인스턴스 없이 사용 가능
- 공통 자원, 메모리 절약

```java
class Example {
    static int staticVar;
    int instanceVar;

    static void staticMethod() {
        System.out.println(staticVar);
    }

    void instanceMethod() {
        System.out.println(staticVar);
        System.out.println(instanceVar);
    }

    static {
        staticVar = 100;
    }
}
```

---

### this와 super 키워드

**this**

- 현재 **객체 자기 자신**을 참조하는 키워드
- 지역 변수와 인스턴스 변수 **이름이 겹칠 때 구분**
- 생성자 오버로딩 호출 가능

```java
class Person {
    private String name;

    public Person(String name) {
        this.name = name;
    }

    public Person() {
        this("Unknown");
    }

    public Person getThis() {
        return this;
    }
}
```

**super**

- **부모 클래스**를 참조
- 부모의 필드, 메서드, 생성자 호출 시 사용
- 오버라이딩된 메서드에서 부모의 원본 메서드 호출 가능
- 주로 초기화 공통 검증 로직이 필요할 때 사용

```java
class Student extends Person {
    private int grade;

    public Student(String name, int grade) {
        super(name); // 부모의 생성자를 호출하는 기능
        this.grade = grade;
    }

    @Override
    public void introduce() {
        super.introduce(); // 부모 클래스의 introduce 메서드를 호출하는 기능
        System.out.println("Grade: " + grade);
    }
}
```

---

## 메서드

- 특정 기능을 수행하는 코드 블럭
- 입력(매개변수) → 처리 → 출력(반환값)

### 메서드 구조

```java
[접근제어자] [static] [final] 반환타입 메서드명(매개변수) [throws 예외] {
    // 메서드 몸체
    return 반환값;
}
```

**메서드 오버로딩 (Overloading)**
- 같은 이름의 메서드를 **다른 매개변수 조합**으로 여러 개 정의 가능
- 메서드명 동일하고 매개변수 개수/타입/순서 달라야 오버로딩 가능

```java
class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public double add(double a, double b) {
        return a + b;
    }

    public int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

**오버라이딩 (Overriding)**  
- **상속 관계**에서 부모 메서드를 자식이 재정의

```java
@Override
void display() {
    System.out.println("재정의된 메서드");
}
```

---

## 예외 처리

### 예외란?

프로그램 실행 중 발생하는 비정상적인 상황

### 예외의 종류

| 분류       | 설명                           | 예시                      |
|------------|--------------------------------|---------------------------|
| Checked    | 컴파일 시 예외 처리 강제       | IOException, SQLException |
| Unchecked  | 런타임 시 발생, 선택적 처리     | NullPointerException, ArithmeticException |

### try-catch-finally 문

```java
try {
    // 예외 발생 가능 코드
} catch (ExceptionType e) {
    // 예외 처리
} finally {
    // 항상 실행 (생략 가능)
}
```

### throws와 throw

```java
// 메서드 선언부에서 예외 전달
public void readFile(String path) throws IOException {
    // ...
}

// 명시적으로 예외 발생
throw new IllegalArgumentException("잘못된 입력");
```

### 사용자 정의 예외

```java
class MyException extends Exception {
    public MyException(String message) {
        super(message);
    }
}

class Test {
    public void check(int value) throws MyException {
        if (value < 0) {
            throw new MyException("음수는 허용되지 않습니다.");
        }
    }
}
```
