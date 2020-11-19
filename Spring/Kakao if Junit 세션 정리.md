2020.11.18

Junit5 라는 새로운 변화를 선택한 이유

- 강한 결합도 (IDE?)
- 부족한 확장성 (하나의 @Runwith 에 여러 Rule을 사용하여 확장, @Runwith 간의 결합 X)
- 많은 책임 (하나의 Jar이 많은 책임을 가지고 있는 상태 - Big Jar)

Junit 5는

- Simple (최대한 단순하게)
- Extension (기능보다 확장성을)

이라는 두가지의 원칙을 가지고 만들어져간다.

```java
// Junit4 : public 접근 제어자가 꼭 필요하다.
@Test
public void testMethod() {
		System.out.println("Do Something");
}

// Junit5 : 패키지 범위에서 실행 가능하다.
@Test 
void testMethod() {
		System.out.println("Do Something");
}
```

기존 확장성 (Runner, MethodRule, TestRule) → Extension 하나로 통합, 조합 가능. 

```java
@ExtendWith, @RegisterExtension, Java의 ServiceLoader 를 통해 등록 가능하다.

Extension 은 선언된 순서대로 등록되고 Class와 Method 에 적용 가능하다.

- 일반적인 확장포인트
ParamerterResolver
ExecutionCondition
TestInstancePostProcessor
TestExecutionExceptionHandler

- 테스트 관련 확장포인트
BeforeAllCallback
	BeforeEachCallback
		BeforeTestExecutionCallBack
		AfterTestExecutionCallBack
	AfterEachCallback
AfterAllCallBack
```

**Junit5 Architecture**

- Vintage (Junit4 와의 호환성을 위해 가지는 모듈)
- Jupiter (개편된 Junit5 모듈)
- Platform (Extension, 다른 라이브러리를 실행하고 관리 가능한 모듈)

Jupiter?

```java
   Junit4      Junit5

@Annotations
BeforeClass   BeforeAll
Before        BeforeEach
Test          Test
After         AfterEach
AfterClass    AfterAll
Category      Tag
Ignore        Disabled

@Assert

- Junit4 에서는 하나의 assert가 실패하면 다음 assert를 실행하지 않았다.
- Junit5 는 assertAll을 통하여서 여러개의 assert를 동시에 실행하고 검증할 수 있다.
- 인자로 넘기는 값을 함수로 사용할 수 있기에, 람다식도 가능하다.

- Junit4 에서는 예외 메세지 검증을 위해 다른 방법을 사용해야 했지만
- Junit5 에서는 assertThrows 를 사용하여 Exception 검증을 쉽게 할 수 있다.

Exception exception = assertThrows(
	SomeClass.class, () -> calculator.divide(1, 0)
);

assertEquals("/ by zero", exception.getMessage());

- Junit4 에서는 Annotation을 통해 테스트 실행 시간을 검증하였지만
- Junit5 에서는 테스트 실행 시간에 대한 aseert 기능이 추가되었다.

assertTimeout(ofSeconds(1), ()-> {Thread.Sleep(5000}; });

// 기대하는 시간이 초과되면 테스트는 즉시 실패하게된다.
assertTimeoutPreemptively(ofSeconds(1), () -> { Thread.sleep(5000); });

@Nested annotation

test 내부에 테스트를 계층적으로 작성할 수 있도록 추가되었다. = BDD 스타일 테스트?

@ParameterizedTest

여러개의 테스트 데이터를 매개변수 형태로 넘길 수 있도록 작성되었다.
@Source Annotation과 같이 동작한다. (Null, Empty, Value, CSV, Enum, Method)

Dynamic Test

기존 테스트는 컴파일 시점에서 검증하고 제한되었지만, 이제는 런타임 환경으로 생성되고 수행이 가능하여졌다.

이를 통해 외부 자원을 활용하고, 렌덤 데이터를 생성하여 활용할 수 있다.
타이틀을 활용하여 조금 더 가독성이 높은 테스트를 작성할 수 있다.

@TestFactory
Stream<DynamicNode> dynamicTests() {
		return Stream.of("hello","world","lob")
						.map(text ->
								dynamicTest("is lob?", () -> assertTrue(text.contains("lob"))
						);
}
```