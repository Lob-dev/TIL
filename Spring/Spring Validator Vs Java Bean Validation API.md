2020.12.03

## **Spring Validator Vs Java Bean Validation API**

<br/>

### **Spring Validator**

**org.springframework.validation.Validator Interface**

Validator 인터페이스를 구현하는 하위 클래스**를** 생성하고 클라이언트 (Application) 에서 

호출하여 사용함으로써 값을 검증할 수 있다.

스프링 부트에서는 이미 구성된 Validator 인스턴스가 존재하며, 해당 인스턴스를 주입받아   사용 함으로써 데이터를 검증할 수 있다.

값에 대한 검증시 잘못된 부분에 대해서는 예외를 발생시키고 Error 객체에 전달함으로써    별도의 처리가 가능하다.

<br/>

### **Java Bean Validation API (JSR-380)**

Bean 유효성 검사를위한 Java API 사양을 말한다.

@NotNull, @Min, @Max, @Size, @NotEmpty... 등

annotation을 필드 값에 설정함으로써, 제약 조건을 정의할 수 있으며, 해당 객체를 사용하는 지점(메서드) 에서 @Vaild 를 붙임으로써 값의 대한 검증을 진행할 수 있다.

유효성 검사가 실패할 경우 MethodArgumentNotValidException를 발생시키며, 스프링은 해당 예외를 HTTP 상태 400 (Bad Request) 로 변환한다고 한다.