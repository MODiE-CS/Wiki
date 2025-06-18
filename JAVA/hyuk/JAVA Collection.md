### 컬렉션 계층구조

```
Collection(I)
├── List(I)
│   ├── ArrayList(C)
│   ├── LinkedList(C)
│   └── Vector(C)
│       └── Stack(C)
├── Set(I)
│   ├── HashSet(C)
│   ├── LinkedHashSet(C)
│   └── SortedSet(I)
│       └── TreeSet(C)
└── Queue(I)
    ├── PriorityQueue(C)
    └── Deque(I)
        └── ArrayDeque(C)

Map(I)
├── HashMap(C)
├── LinkedHashMap(C)
├── Hashtable(C)
│   └── Properties(C)
└── SortedMap(I)
    └── TreeMap(C)
```

### 컬렉션 인터페이스 특징

**List**
- 순서가 있는 데이터 집합
- 중복 허용
- 인덱스 기반 접근 가능
- 구현체: ArrayList, LinkedList, Vector

**Set**
- 순서가 없는 데이터 집합 (LinkedHashSet 제외)
- 중복 불허
- 수학의 집합 개념
- 구현체: HashSet, LinkedHashSet, TreeSet

**Map**
- 키-값 쌍으로 구성
- 키는 중복 불허, 값은 중복 허용
- 키를 통한 빠른 검색
- 구현체: HashMap, LinkedHashMap, TreeMap, Hashtable

**Queue**
- FIFO(First In First Out) 구조
- 데이터 입출력 순서가 중요한 경우
- 구현체: LinkedList, PriorityQueue, ArrayDeque

**Stack**
- LIFO(Last In First Out) 구조
- Vector를 상속받음 (현재는 Deque 사용 권장)

## List 구현체 비교

### ArrayList vs LinkedList vs Vector

| 구분           | ArrayList        | LinkedList       | Vector          |
| ------------ | ---------------- | ---------------- | --------------- |
| 자료구조         | 동적 배열            | 이중 연결 리스트        | 동적 배열           |
| 접근 속도        | O(1)             | O(n)             | O(1)            |
| 삽입/삭제 (중간)   | O(n)             | O(1)             | O(n)            |
| 삽입/삭제 (끝)    | O(1)             | O(1)             | O(1)            |
| 메모리 사용       | 적음               | 많음 (포인터 오버헤드)    | 적음              |
| 동기화          | 동기화 안됨          | 동기화 안됨          | 동기화됨           |
| 크기 증가        | 50% 증가          | 노드별 할당          | 100% 증가        |
| 사용 시기        | 조회가 많은 경우       | 삽입/삭제가 많은 경우    | 멀티스레드 환경 (권장하지 않음) |
`현재는 Vector 대신 Collections.synchronizedList() 사용`


### Priority Queue

우선순위 큐는 들어간 순서에 상관 없이 우선순위가 높은 데이터를 먼저 꺼내기 위해 만들어진 자료구조
-> 배열, 연결리스트, 힙 다양한 방식으로 구현 가능하지만 주로 워스트 케이스가 logN을 보장하는 힙 방식을 주로 사용

## Set

### HashSet vs LinkedHashSet vs TreeSet

| 구분       | HashSet          | LinkedHashSet    | TreeSet          |
| -------- | ---------------- | ---------------- | ---------------- |
| 자료구조     | 해시 테이블           | 해시 테이블 + 연결 리스트 | Red-Black Tree   |
| 순서 보장    | 보장 안함            | 삽입 순서 보장        | 정렬 순서 보장        |
| 성능       | O(1)             | O(1)             | O(log n)         |
| null 허용  | 허용 (1개)          | 허용 (1개)          | 불허용              |
| 동기화      | 동기화 안됨          | 동기화 안됨          | 동기화 안됨          |
| 사용 시기    | 빠른 검색이 필요한 경우    | 순서도 중요한 경우      | 정렬된 순서가 필요한 경우  |

### Set과 Map의 중복 검사

**중복 검사 과정**
1. `hashCode()` 메서드로 해시코드 비교
2. 해시코드가 같으면 `equals()` 메서드로 재비교
3. 둘 다 같으면 중복 객체로 판단

## Map

### HashMap vs LinkedHashMap vs TreeMap vs Hashtable

| 구분       | HashMap          | LinkedHashMap    | TreeMap          | Hashtable        |
| -------- | ---------------- | ---------------- | ---------------- | ---------------- |
| 자료구조     | 해시 테이블           | 해시 테이블 + 연결 리스트 | Red-Black Tree   | 해시 테이블           |
| 순서 보장    | 보장 안함            | 삽입/접근 순서 보장     | 키 정렬 순서 보장      | 보장 안함            |
| 성능       | O(1)             | O(1)             | O(log n)         | O(1)             |
| null 허용  | key, value 모두 허용 | key, value 모두 허용 | value만 허용       | key, value 모두 불허용 |
| 동기화      | 동기화 안됨          | 동기화 안됨          | 동기화 안됨          | 동기화됨            |
| 상속       | AbstractMap      | HashMap          | AbstractMap      | Dictionary       |
| 사용 환경    | 싱글스레드           | 싱글스레드           | 정렬 필요시          | 멀티스레드 (비권장)     |

##### 해시테이블

해시 테이블은 키, 벨류로 데이터를 저장하는 자료구조로, 빠른 데이터 검색 특징을 가진 자료구조
-> 각 키가 해시함수에 의해 고유한 index를 가지게되므로 원하는 데이터를 검색하는 시간 복잡도가 O(1)
-> 하지만 Index 값이 충돌할 경우 체이닝에 연결된 리스트들까지 검색해야하므로 O(n)까지 증가 가능

- HashMap : 병렬처리를 하지 않을 때 Thread-safe하지 않음, Null 허용
- HashTable : 병렬처리를 할 때 Thread-safe하지만 느려서 잘 사용하지 않음, Null 비허용
- ConcurrentMap : JAVA에서 사용하는 병렬처리용 HashMap으로 주로 이걸 사용


#### 해시 충돌 (Hash Collision)

서로 다른 키를 동일한 인덱스로 변환한 경우를 의미

##### Separate Chaining (분리 연결법)
- 충돌이 발생한 인덱스에 단일 값이 아닌 연결리스트를 두어 같은 해시 인덱스에 해당하는 값들을 체이닝하여 저장하는 방식
- ```hashTable[5] → [ ("key1", value1) → ("key2", value2) → ... ] ```
- 장점
	- 해시 테이블의 크기보다 더 많은 요소 저장 가능
	- 충돌이 많을 경우에도 안정적인 동작
- 단점
	- 포인터 및 리스트로 인해 메모리 오버헤드 발생
	- 충돌이 많을 경우 탐색 속도저하

##### Open Addressing (개방 주소법)
- 충돌 시 해시 테이블 내의 다른 빈 인덱스를 찾아 저장하는 방식
- 선형 탐색, 제곱 탐색, 이중  해싱 등의 방법으로 빈 인덱스를 찾는 방식
- 장점
	- 별도 구조 없이 배열만 사용해 공간 절약
	- 메모리 접근 효율이 좋음
- 단점
	- 클러스터링(데이터 뭉침) 문제 발생으로 성능 저하 가능성 존재
	- 테이블이 꽉 차면 저장이 불가능해 Load Factor 관리 필요

- Load Factor : 해시 테이블의 채워진 정도를 나타내는 지표
	-> 저장된 데이터 수 / 해시 테이블의 크기
	- 낮을 수록 충돌 가능성 낮고 평균 탐색 시간이 빠름
	- 높을 수록 충돌 가능성 높고 탐색/삽입/삭제 성능 저하
	-> 임계값을 정하고 초과시 리사이징하는 리사이징 트리거로 사용됨
	
	개방 주소 방법(Open Addressing)에서 중간의 원소를 단순히 삭제하면, 이후 탐색 시 그 자리에 아무것도 없다고 판단하여 **탐색이 조기에 종료되는 문제**가 발생
	-> 삭제된 자리는 실제 삭제가 아닌 **"삭제 마커(deleted flag)"**를 표시하여 탐색은 계속 진행되도록 하고 삽입 시에는 해당 자리를 재활용할 수 있도록 처리하는 방식 사용


### 해시충돌이 발생하기 전에 이를 완화하는 방법은 무엇이 있을까?

1.  **좋은 해시 함수 설계** : 키의 분포를 고르게 만들 수 있도록 충돌 가능성을 최소화하는 해시 함수를 사용하는 것이 중요

2. **해시 테이블의 크기를 소수(prime number)로 설정하거나, 충분히 크게 유지**하여 해시 값이 균등하게 분포되도록 설정

3.  **적절한 리사이징과 로드 팩터 관리**를 통해 해시 버킷당 항목 수를 제한하고 충돌 가능성을 줄일 수 있음


```
💡 해시 충돌 처리
- Java 8 이전: 연결 리스트 (Separate Chaining)
- Java 8 이후: 연결 리스트 → 레드-블랙 트리 변환 (성능 개선)
```

## Iterator와 ListIterator

Collection 요소를 순차적으로 탐색할 수 있도록 도와주는 반복자(Iterator)

### Iterator와 ListIterator 비교

| 구분         | Iterator     | ListIterator    |
| ---------- | ------------ | --------------- |
| 방향         | 단방향 (forward) | 양방향 (bidirectional) |
| 주요 메서드     | hasNext(), next(), remove() | add(), set(), previousIndex(), nextIndex() |
| 사용 가능 컬렉션 | 모든 Collection | List만           |
| 위치 정보      | 제공 안함        | previousIndex(), nextIndex() 제공 |
| 수정 기능      | remove()만    | add(), set(), remove() |
| 시작 위치      | 처음부터만        | 임의 위치부터 가능     |

### fail-fast vs fail-safe

Collection을 순회하면서 구조 변경(요소 추가/삭제 등)이 일어날 때 어떤 동작을 하는가에 대한 개념
- fail-fast : 반복 중 Collection이 변경되면 예외를 던지는 방식
- fail-safe : 반복 중 Collection이 변경되어도 복사본을 기반으로 반복해서 정상 동작하는 방식

| 구분        | fail-fast            | fail-safe                |
| --------- | -------------------- | ------------------------ |
| 동작 방식     | 수정 감지 시 즉시 예외 발생     | 복사본에서 반복              |
| 예외        | ConcurrentModificationException | 예외 발생 안함         |
| 성능        | 빠름                   | 느림 (복사 오버헤드)          |
| 메모리 사용    | 적음                   | 많음                     |
| 사용 컬렉션    | ArrayList, HashMap 등 | CopyOnWriteArrayList 등  |

```java
import java.util.*;
import java.util.concurrent.CopyOnWriteArrayList;

public class IteratorExample {
    public static void main(String[] args) {
        // fail-fast 예시 (ArrayList)
        List<String> failFastList = new ArrayList<>(Arrays.asList("A", "B", "C", "D"));
        
        try {
            for (String item : failFastList) {
                if ("B".equals(item)) {
                    failFastList.add("X"); // ConcurrentModificationException 발생
                }
            }
        } catch (ConcurrentModificationException e) {
            System.out.println("fail-fast: ConcurrentModificationException 발생");
        }
        
        // 올바른 삭제 방법
        List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C", "D", "E"));
        Iterator<String> iter = list.iterator();
        while (iter.hasNext()) {
            String element = iter.next();
            if ("C".equals(element)) {
                iter.remove(); // 안전한 삭제
            }
        }
        System.out.println("안전한 삭제 후: " + list);
        
        // ListIterator 양방향 탐색
        ListIterator<String> listIter = list.listIterator();
        while (listIter.hasNext()) {
            String element = listIter.next();
            if ("B".equals(element)) {
                listIter.set("Modified B"); // 현재 요소 수정
                listIter.add("New Element"); // 새 요소 추가
            }
        }
        
        // 역방향 탐색
        while (listIter.hasPrevious()) {
            String element = listIter.previous();
            System.out.println("역방향: " + element);
        }
    }
}
```

## Comparable vs Comparator

Java에서 정렬을 구현할 때 사용되는 인터페이스

### Comparable vs Comparator 선택 가이드

| 상황                     | 선택              | 이유                    |
| ---------------------- | --------------- | --------------------- |
| 클래스에 자연스러운 순서가 있는 경우   | Comparable      | 기본 정렬 기준 제공          |
| 여러 가지 정렬 기준이 필요한 경우    | Comparator      | 유연한 정렬 기준 제공         |
| 외부 라이브러리 클래스를 정렬해야 하는 경우 | Comparator      | 소스 코드 수정 불가          |
| 임시적인 정렬이 필요한 경우       | Comparator      | 일회성 사용               |
| 컬렉션별로 다른 정렬이 필요한 경우   | Comparator      | 상황에 맞는 정렬 기준 적용      |

```java
import java.util.*;

// Comparable 구현
class Student implements Comparable<Student> {
    private String name;
    private int score;
    
    public Student(String name, int score) {
        this.name = name;
        this.score = score;
    }
    
    @Override
    public int compareTo(Student other) {
        return Integer.compare(other.score, this.score); // 점수 내림차순
    }
    
    public String getName() { return name; }
    public int getScore() { return score; }
    
    @Override
    public String toString() {
        return name + "(" + score + "점)";
    }
}

public class ComparatorExample {
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("김철수", 85),
            new Student("이영희", 92),
            new Student("박민수", 78)
        );
        
        // Comparable 자연 순서 정렬
        Collections.sort(students);
        System.out.println("자연 순서 (점수 내림차순): " + students);
        
        // Comparator로 다양한 정렬
        students.sort(Comparator.comparing(Student::getName)); // 이름순
        System.out.println("이름순: " + students);
        
        students.sort(Comparator.comparingInt(Student::getScore)); // 점수 오름차순
        System.out.println("점수 오름차순: " + students);
        
        // 복합 정렬
        students.sort(Comparator.comparingInt(Student::getScore)
                               .thenComparing(Student::getName));
        System.out.println("점수 → 이름순: " + students);
    }
}
```

## 성능 비교와 선택 가이드

### 시간 복잡도 비교

| 구현체        | 접근       | 검색       | 삽입       | 삭제       | 특징               |
| ---------- | -------- | -------- | -------- | -------- | ---------------- |
| ArrayList  | O(1)     | O(n)     | O(n)     | O(n)     | 인덱스 기반 빠른 접근     |
| LinkedList | O(n)     | O(n)     | O(1)     | O(1)     | 노드 기반 빠른 삽입/삭제   |
| HashSet    | -        | O(1)     | O(1)     | O(1)     | 해시 기반 빠른 연산      |
| TreeSet    | -        | O(log n) | O(log n) | O(log n) | 정렬된 순서, 범위 검색 가능 |
| HashMap    | O(1)     | O(1)     | O(1)     | O(1)     | 해시 기반 빠른 키-값 접근  |
| TreeMap    | O(log n) | O(log n) | O(log n) | O(log n) | 정렬된 키, 범위 검색 가능  |

### 컬렉션 선택 가이드

**List 선택 기준**
- **ArrayList**: 조회가 빈번한 경우, 메모리 효율성이 중요한 경우
- **LinkedList**: 삽입/삭제가 빈번한 경우, 큐/데크로 사용하는 경우
- **Vector**: 멀티스레드 환경 (현재는 Collections.synchronizedList() 권장)

**Set 선택 기준**
- **HashSet**: 빠른 검색이 필요한 경우, 순서가 중요하지 않은 경우
- **LinkedHashSet**: 삽입 순서를 유지하면서 빠른 검색이 필요한 경우
- **TreeSet**: 정렬된 순서가 필요한 경우, 범위 검색이 필요한 경우

**Map 선택 기준**
- **HashMap**: 빠른 검색이 필요한 경우, 순서가 중요하지 않은 경우
- **LinkedHashMap**: 삽입 순서나 접근 순서를 유지해야 하는 경우
- **TreeMap**: 키의 정렬 순서가 필요한 경우, 범위 검색이 필요한 경우
- **Hashtable**: 레거시 코드와의 호환성 (HashMap + Collections.synchronizedMap() 권장)
