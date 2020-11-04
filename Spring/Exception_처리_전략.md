2020.11.04

<br/>

Exception 처리 전략

<br/>

### **예외 복구**

- **예외상황을 파악하고 문제를 해결해서 정상 상태로 돌려놓는 것을 말한다.**
    - 사용자에게 **예외 상황을 안내**하고 **다른 파일, 요청을 보내게끔** 하여 해결할 수 있다.
    - 즉 **기존 작업 흐름이 불가능**하다면 **다른 작업의 흐름으로 유도**하는 것이다.
    - **어떤 외부 서버, 데이터 베이스와 연결 실패의 경우에는 재시도를 할 수 있다.**
        - 일정 대기 시간, 조건 만큼 재시도를 반복한다. **→ 복구 실패 시 예외 발생**

            ```jsx
            final int RETRYCOUNT = 10;

            public Object someMethod() {
                int Retry = RETRYCOUNT;
                while(Retry > 0) { // 연결 시도의 반복 
                    try {
                        // Server 연결...
                    } catch(SomeException e) {
                        // 연결 실패...
                    } finally {
            						Retry -= 1;
                        // Finally Do Something -> 정리 작업 등
                    }
                }
                // 최대 재시도 횟수를 넘기면 직접 예외를 발생시킨다.
                throw new RetryFailedException();
            }
            ```

<br/>

### **예외 회피**

- 예외가 발생한 지점에서 처리하지 않고 해당 메서드를 호출한 객체에게 처리를 위임한다
    - **콜백이나 템플릿처럼 긴밀하게 역활을 분담하고 있지 않다면, 무책임한 회피이다.**
    - **DAO 가 Service, Controller에게 예외를 회피하였다고 하면 처리를 할 수 있을까?**
        - 예외를 회피하는 것은 예외 복구와 같이 의도가 분명하여야 한다.

    ```jsx
    // 1
    public void someMethod() throws Exception {
    		// Do Something
    }

    // 2
    public void someMethod() throws Exception {
    		try {
    		    method.someMethod();
    		}
    		catch(Exception e) {
    				// log 출력
    		    throw e;
    		}
    }

    ----------------------------------------------------
    // 어떠한 객체 코드
    // Checked Exception의 경우 호출한 곳에서 처리 로직을 작성하지 않으면 컴파일 불가
    // Checked Exception을 Runtime으로 전환하면 트랜잭션 롤백 가능
    		try {
    		    method.someMethod();
    		}
    		catch(Exception e) {
    		    throw new CustomRuntimeException(e);
    		}
    ```

<br/>

### **예외 전환**

예외 회피와 비슷하게 메서드 밖으로 예외를 던지지만, **적절한 예외로 전환하여 넘기는 방법**

**복구해서 정상적인 상태로 만들 수 없기에 사용하게 된다.**

- **Checked Exception → UnChecked Exception (Runtime) 변환**
    - **비즈니스 로직과 관련된 예외나 지금 처리할 수 없는 예외에 대해서 포장한다.**
- **Some Exception → Custom Exception 변환 등**
    - **내부에서 발생한 예외를 적절한 의미로 부여해주기 위하여 사용하기도 한다.**
        - DuplicateUserIdException...

<br/>

### **예외 포장**

```jsx
try {
    // Do Something
}
catch(SQLException e) {
		// Do Something
    throw new RuntimeException(e);
}
```

<br/>

### **중첩 예외**

```jsx
try {
    // Do Something
}
catch(SQLException e) {
		// Do Something
    throw new DuplicateUserIdException().initCause(e);
}
```