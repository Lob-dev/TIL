2020.10.02

<br/>

Java HashMap 의 해시 충돌 구조?

<br/>

**Perfect Hash Functions**

- 동일, 동등하지 않은 객체가 존재할 때, 두 객체의 hashcode 값이 다르다면 이때 사용하는 Hashcode를 **Perfect Hash Functions** 라고 한다.
- 구별되는 객체가 적거나 객체의 Value 자체를 HashCode로 사용하면, **Perfect Hash Functions** 를 만족 시킬 수 있지만 POJO 객체나 String의 경우에는 어렵다. (동등한 값을 가질 수 있고, 여러개가 존재 가능)
- HashMap에서 사용하는 HashCode의 반환 값은 32bit의 정숫 값이며, 이것보다 많은 객체가 생성될 수 있고, 2^32 만큼의 공간을 미리 할당하는 것은 큰 리스크이다.
- 결국 HashMap은 메모리를 절약하기 위해 정수 표현 범위보다 작은 원소가 있는 배열을 사용하게 된다.

<br/>

**Separate Chaining**

- Java 에서는 해시 충돌을 해결하기 위해 Separate Chaining 라는 방식을 사용하며, 해당 방식은 같은 HashCode 값을 가질 경우 해당 Bucket 을 참조하는 링크드 리스트를 만들어서 데이터를 저장하며, JDK 8 이후에는 해당 Bucket 의 검색 효율성을 위해 Red-Black Tree로 변경하여 $LogN/M (m = 충돌한 요소의 수)$ 의 성능을 내게끔 한다.

    **TREEIFY_THRESHOLD = 8 → 요소의 값이 8 이상이면 트리로 변경**

    **UNTREEIFY_THRESHOLD = 6 → 요소의 값이 6 이하이면 리스트로 변경**

    트리 순회 시 사용하는 대소 판단 기준은 해시 함수 값이다. 해시 값을 대소 판단 기준으로 사용하면 Total Ordering에 문제가 생기는데, Java 8 HashMap에서는 이를 tieBreakOrder() 메서드로 해결한다.

    <img src="./../image/16eBeaqTti8MxWPsw4xBgw.png" width="90%"></img>

**해당 방식의 장점?**

1) 한정된 저장소(Bucket)을 효율적으로 사용할 수 있다.

2) 해시 함수(Hash Function)을 선택하는 중요성이 상대적으로 적다.

3) 상대적으로 적은 메모리를 사용한다. 미리 공간을 잡아 놓을 필요가 없다.

**해당 방식의 단점?**

1) 한 Hash에 자료들이 계속 연결된다면(쏠림 현상) 검색 효율을 낮출 수 있다.

2) 외부 저장 공간을 사용한다.

3) 외부 저장 공간 작업을 추가로 해야 한다.

<br/>

### HashMap 의 loadFactor ?

<br/>

- **InitialCapacity 는 bucket 의 수를 뜻한다.**
    - ReSizeing, ReHashing 빈도를 줄이기 위해서 Capacity를 크게 잡는다.
- **loadFactor 는 용량에 따른 부하율을 말한다.**
    - bucket Size 안에서 데이터가 loadFactor의 기준치보다 크다면 Resizing이 된다.
    - **ReSizeing?**
        - 버킷이 기준치 만큼 데이터가 채워졌을 경우 각 버킷에 해시 충돌로 인하여 생겨난 연결리스트의 길이가 늘어나 검색 성능이 떨어지기에 버킷의 수를 늘려주는 것을 말한다.
        - **ReSizeing이 발생한다면, ReHashing 도 발생하게 된다.**
            - **ReHashing?**
                - 내부에 저장된 데이터들을 다시 해싱하여 저장하는 작업을 말한다.
        - JDK 8부터는 해시 충돌 시 한 버킷에 8개 이상의 값이 채워지면, **Balanced Tree로 변형**
    - 즉 **loadFactor** 는 데이터의 검색 성능을 위해 지정한 기준 값이다.

<br/>

### HashMap과 HashTable 의 차이 

<br/>

- **HashTable** 은 **동기화**가 보장된다.
    - **내부 동작 메서드에 synchronized 가 적용되어있다.**
    - **한번에 하나의 쓰레드만 메서드에 접근할 수 있기에 성능적으로 문제가 있다.**
- **HashMap**은 **Fail-Fast Iterator**를 반환하고**, HashTable**은 **Enumeration**를 반환한다.
    - **Enumeration**은 Collection 인터페이스가 나오기 이전에 사용되던 것이다.
        - 순차적 접근 시 콜렉션 객체에 변경이 일어나도 이를 무시하고, 끝까지 동작한다.
    - **Iterator**는 순차적 접근이 끝나기 전에 객체에 변경이 일어날 경우 예외를 반환한다.
        - **Fail-Fast Iterator**는 여러 쓰레드가 **동일한 자원의 수정을 발생**시키면 **일관성을 보장하기 위하여 ConcurrentModificationException**를 **발생**시킨다.
- **HashMap의 경우 Null 값이 들어왔다면 0으로 변경하여 Hashing 한다. (Null도 저장.)**

<br/>

- **HashTable 을 사용하지 않는 이유?**
    - **성능 상의 문제.**
        - 모든 메서드에 **synchronized** 가 붙어 있기 때문에 모든 상황에서 하나의 쓰레드만 접근할 수 있다.
            - **모든 상황에서 Thread-safe 할 이유는 없다.**
    - **컬렉션 순회동안 데이터의 일관성을 보장.**
        - **Iterator 와 Enumeration 의 차이.**
    - **Double Hashing (: supplement hash function) 미지원**
        - Double Hashing이란 Hashcode를 통한 Key 값 변환시 충돌 가능성을 줄이기 위해 사용.
        - X.hashCode() % M 연산시 M은 정수이기에 소수일 때보다 분포가 적을 수 밖에 없다.
            - supplement hash function 은 해당 Key의 해시 값을 변형하여 분포를 늘리고, 충돌 가능성을 줄이게 된다.

            ```jsx
            // 상위 16비트 값을 XOR 연산하는 매우 단순한 형태의 보조 해시 함수를 사용한다.
            static final int hash(Object key) { 
            		int h; return (key == null) ? 0 : 
            											(h = key.hashCode()) ^ (h >>> 16); 
            }
            ```

            간단한 해시 함수를 사용하는 이유로는.

            - 해시 충돌 시 발생할 수 있는 성능 문제가 완화되었고 (리스트 → 트리)
            - 최근의 해시 함수는 균등 분포가 잘 되게 만들어지는 경향이 많기에 기존 해시 함수의 효과가 크지 않았다.