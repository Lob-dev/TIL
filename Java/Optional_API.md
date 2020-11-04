2020.10.30

<br/>

Optional API

<br/>

Null 값을 리턴하는 것이 아니라, 해당 객체를 사용할 수 있는지 검증 가능한 API.

- 값이 내부에 존재할 수도 있고, 없을 수도 있다. (Null)
- NPE를 방지하기 위해 사용된다. (실수를 방지한다.)
- Exception 처리와 비슷한 취지를 가진다.
- 스프링의 최종 연산도 상당수가 옵셔널을 반환한다.

<br/>

**작업 중 특별한 상황에서 값을 제대로 리턴할 수 없는 경우.**

- 예외를 던진다 (Stacktrace)
- Null을 리턴한다. ( Runtime Exception + 다음 로직에 무슨일이 일어날 지 모른다.)
- Optional 리턴한다. (빈 값임을 알려주고 Null 처리를 강제한다.)

**리턴 값을 반환할 때만 사용하는 것이 좋다. (메서드 반환 값이 Optional)**

Primitive Type 을 위한 Optional이 존재한다. (OptionalInt, OptionalLong)

<br/>

**Optional 만들기**

- **Optional.of()**
- **Optional.ofNullable()**
- **Optional.empty()**

<br/>

**요소 값 검증**

- **isPresent()**
- **isEmpty (JDK 11)**

<br/>

**값 가져오기**

- **get()**
    - **빈 값을 꺼내온 경우?**

<br/>

**값이 존재한다면 가져오고 없다면 empty를 리턴**

- **ofElse(T)**
    - **없다면 값을 만들어 리턴하라. (초기 값을 제공)**
        - **orElseGet(Supplier)**
    - **없다면 예외를 반환하라.**
        - **orElseThrow()**

<br/>

**Optional 필터링**

- **filter(Predicate)**
    - **startWith("문자열") → 문자열로 시작하는 요소.**

<br/>

**Optional 값 변환하기**

- **map(Function)**
- **flatMap(Function)**