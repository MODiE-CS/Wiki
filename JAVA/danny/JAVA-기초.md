# 메서드

자바에서 메서드는 클래스 내에 정의되어야 하며 어떤 작업을 수행하는 명령문의 집합입니다. 다른 언어에서 말하는 '함수'와 유사한 개념이지만, 자바에서는 반드시 클래스 내부에 존재하기 때문에 메서드라고 부릅니다.
메서드를 정의할 때는 반환 타입, 메서드 이름, 매개변수, 본문으로 구성되며, 예를 들어 다음과 같이 정의할 수 있습니다.

```java
public int add(int a, int b) {
    return a + b;
}
```

이 메서드는 두 개의 정수를 받아서 더한 결과를 반환합니다. 반환 타입은 int, 메서드 이름은 add, 매개변수는 a와 b입니다. 반환이 필요 없는 경우에는 void를 사용합니다.
또한, 여러 메서드를 하나의 클래스 안에 정의하고 이를 통해 코드의 재사용성을 높일 수 있습니다.

## 메서드 정의 요소

메서드를 정의할 때 다음과 같은 요소들이 필요합니다:

* **접근 제어자**: public, private, protected 등. 외부에서 이 메서드에 접근 가능한지를 결정합니다.
* **반환 타입**: 메서드가 실행된 후 반환하는 값의 자료형입니다. 반환 값이 없다면 void를 사용합니다.
* **메서드 이름**: 소문자로 시작하는 카멜 표기법을 사용합니다.
* **매개변수**: 0개 이상이며, 각 매개변수는 타입과 이름으로 구성됩니다.
* **메서드 본문**: {} 중괄호로 감싸며, 실제로 실행될 코드들이 들어갑니다.

예시:

```java
public static String greet(String name) {
    return "Hello, " + name;
}
```

## 메서드 호출

정적(static) 메서드는 클래스 이름으로 바로 호출할 수 있고, 인스턴스 메서드는 객체를 생성한 후 호출해야 합니다.

```java
public class Calculator {
    public int add(int x, int y) {
        return x + y;
    }
}

public class Main {
    public static void main(String[] args) {
        Calculator calc = new Calculator();
        int result = calc.add(3, 5);
        System.out.println(result);
    }
}
```

## 문자열 처리

자바에서는 문자열 연결을 + 연산자로 수행할 수 있으며, String 객체는 불변(immutable) 특성을 가집니다.

```java
String str1 = "Hello";
String str2 = "World";
String result = str1 + " " + str2;
```

자주 사용하는 문자열 메서드는 다음과 같습니다:

* `substring(int start, int end)` : 문자열 일부 추출
* `indexOf(String s)` : 특정 문자열의 시작 위치 반환
* `length()` : 문자열의 길이
* `charAt(int index)` : 특정 위치의 문자 반환

문자열을 자주 수정해야 할 경우 `StringBuilder` 또는 `StringBuffer`를 사용하는 것이 좋습니다.

```java
StringBuilder sb = new StringBuilder();
sb.append("Hello").append(" ").append("Java");
System.out.println(sb.toString());
```

## 배열

배열은 같은 타입의 데이터를 순서대로 저장할 수 있는 자료구조입니다. 배열의 길이는 고정되며, 인덱스는 0부터 시작합니다.

선언과 동시에 초기화:

```java
int[] numbers = {1, 2, 3, 4, 5};
```

배열의 크기 지정 후 초기화:

```java
String[] names = new String[3];
names[0] = "철수";
names[1] = "영희";
names[2] = "민수";
```

주의할 점은 존재하지 않는 인덱스를 참조하면 `ArrayIndexOutOfBoundsException`이 발생합니다.

## 기본 데이터 타입

자바의 기본 데이터 타입(primitive type)은 다음과 같습니다:

| 타입      | 크기   | 범위/특성             |
| ------- | ---- | ----------------- |
| byte    | 1바이트 | -128 \~ 127 정수    |
| short   | 2바이트 | -32,768 \~ 32,767 |
| int     | 4바이트 | 기본 정수형            |
| long    | 8바이트 | 더 큰 정수형           |
| float   | 4바이트 | 실수 (소수점 7자리)      |
| double  | 8바이트 | 실수 (소수점 15자리)     |
| char    | 2바이트 | 하나의 문자            |
| boolean | 1바이트 | true / false      |

이들은 객체가 아닌 실제 값을 저장하므로, 연산이 빠르고 메모리 효율이 좋습니다. 기본값은 정수형은 0, 실수형은 0.0, char는 '\u0000', boolean은 false입니다.

## 변수 선언과 초기화

변수는 데이터를 저장할 공간으로, 선언 시에는 타입과 변수명을 지정하고, 초기화를 통해 값을 할당할 수 있습니다.

```java
int age = 20;
boolean isStudent = true;
String name = "예찬";
```

한 줄에 여러 변수를 선언할 수도 있습니다:

```java
int x = 10, y = 20;
```

## 연산자

자바에서 사용되는 주요 산술 연산자는 다음과 같습니다:

* `+` : 덧셈
* `-` : 뺄셈
* `*` : 곱셈
* `/` : 나눗셈
* `%` : 나머지

```java
System.out.println(10 + 3);  // 13
System.out.println(10 / 3);  // 3 (정수 나눗셈)
System.out.println(10 / 3.0); // 3.3333 (실수 나눗셈)
```

증감 연산자:

```java
int a = 5;
a++;  // 6
a--;  // 5
```

## 입력과 출력

입력: Scanner 클래스를 사용하여 콘솔로부터 입력을 받을 수 있습니다.

```java
Scanner sc = new Scanner(System.in);
System.out.print("이름을 입력하세요: ");
String name = sc.nextLine();
```

출력: System.out.print(), System.out.println(), System.out.printf() 등을 사용합니다.

```java
System.out.println("Hello World");
System.out.printf("이름: %s, 나이: %d\n", name, age);
```

## 조건문

조건에 따라 흐름을 분기할 수 있습니다.

```java
int score = 85;
if (score >= 90) {
    System.out.println("A");
} else if (score >= 80) {
    System.out.println("B");
} else {
    System.out.println("C");
}
```

복잡한 경우에는 switch문도 사용합니다.

```java
int month = 3;
switch (month) {
    case 3:
    case 4:
    case 5:
        System.out.println("봄입니다");
        break;
    default:
        System.out.println("기타 계절입니다");
}
```

## 중첩 if 문과 가독성 향상 방법

중첩 if란 if문 안에 또 다른 if문이 있는 구조입니다.

```java
if (age >= 18) {
    if (hasID) {
        System.out.println("입장 가능");
    }
}
```

가독성 높이려면?

* **논리 연산자 활용**

  * 중첩 대신 한 줄에 조건 통합
  * `if (age >= 18 && hasID)`

* **메서드로 분리**

  * 복잡한 조건은 메서드로 뺌
  * `if (canEnter(age, hasID))`

* **else if 사용**

  * 순차적 분기엔 else if 사용
  * 위 예시 참고

## 주석

* `//` : 한 줄 주석
* `/* */` : 여러 줄 주석
* `/** */` : Javadoc 문서용 주석

주석은 코드 설명, 디버깅, 협업에 큰 도움이 되므로 적극 활용하는 습관을 들이는 것이 좋습니다.

## 클래스와 객체

클래스는 객체를 생성하기 위한 틀(설계도)이고, 객체는 해당 틀을 통해 실제로 메모리에 생성된 인스턴스를 의미합니다.

```java
public class Person {
    String name;
    int age;

    void introduce() {
        System.out.println("저는 " + name + "이고, 나이는 " + age + "세입니다.");
    }
}

Person p = new Person();
p.name = "예찬";
p.age = 25;
p.introduce();
```

## 추상화

추상화는 복잡한 내부 구현을 감추고, 필요한 기능만 외부에 제공하는 것입니다. 예를 들어 자동차를 운전할 때 내부 엔진 구조를 몰라도 핸들, 브레이크 등의 조작만으로 운전할 수 있는 것이 추상화입니다.

## IDE

자바 개발에는 다음과 같은 IDE(통합 개발 환경)를 많이 사용합니다:

* **Eclipse**: 오픈소스, 빠르고 가벼움
* **IntelliJ IDEA**: 강력한 자동완성과 코드 분석 기능, 상용/커뮤니티 버전 존재

IDE를 사용하면 자동완성, 디버깅, 라이브러리 관리 등 개발의 편의성이 크게 향상됩니다.
