2020.11.04

<br/>

### Spring Error Response 전략

클라이언트에서 예외 처리를 항상 동일한 로직으로 처리해야 하기에, Error Response 객체는 항상 동일한 Response를 가져야 한다.

1. **ExceptionHandler 의 반환 값을 ResponseEntity<ErrorResponse> 로 통일하자.**
2. **하나의 Error Response Object를 선언하여 사용한다.**
3. **내부에 Error Code 를 Enum Class 로 정의한다.**
4. **반환 값을 Key, Value로 작성하는 것보단 객체 자체를 반환하도록 하자.**

    ```jsx
    new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
    ```