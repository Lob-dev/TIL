2020.10.30

### Functional Interface

<br/>

**Java 주요 Functional Interface**

- **Function<T,R>**
    - **T라는 타입을 받아서 R 타입으로 리턴하는 함수 인터페이스**
        - map
    - **함수 조합용 메서드**
        - andThen : 앞선 함수의 반환 값을 가지고 뒤의 함수를 적용
        - compose : 들어온 인자 값을 가지고 먼저 뒤의 호출되는 함수를 적용

- **BiFunction<T,U,R>**
    - 두개의 타입인 T,U를 받아서 R이라는 타입으로 반환하는 함수 인터페이스

- **UnaryOperator <T,R> (Function을 상속받음)**
    - T 타입을 받아서 R로 반환하는 함수 인터페이스

- **Consumer<T>**
    - T라는 타입을 받아서 아무것도 반환하지 않는 함수 인터페이스
        - void Aceept(T t)

- **Supplier<T>**
    - T 타입의 값을 제공하는 함수 인터페이스 ( 인자가 아니라 반환 타입을 지정. )
        - Supplier<Integer> get10 = () → 10;
        - isOdd
        - 함수 조합용 메서드
            - And
            - Or
            - Negate

- **Predicate<T>**
    - T라는 인자 값을 받아서 Boolean 값을 제공하는 함수 인터페이스