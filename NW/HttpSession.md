2020.01.21

<br/>

### **Session**

---

웹 애플리케이션에서의 세션 상태는 사용자 정보를 의미한다. 이러한 세션은 처음 요청부터 마지막 상호 작용까지 일정 기간동안만 메모리에 유지되는 정보이다.

세션은 Java 객체로써 관리되며 식별값을 통해 관리된다. 

<br/>

### **Session ID**

---

Web Container가 요청을 식별하기 위해 생성하는 임의의 식별값을 말한다.

이 값은 서버의 메모리, 클라이언트의 스토리지에 저장되며 이를 통해 사용자를 특정하고 특정 로직을 실행할 수 있다.

*이때 저장되는 데이터에 대한 타입은 Memory cookie 이며, 이는 해당 세션 종료시 소멸된다.*

<br/>

### **Session 사용법**

---

- 세션에 유저 정보 바인딩 (개인 정보가 아닌 것 위주로 : PK ID, ROLE 등)
- 세션 식별
- 세션 생성 시간, 마지막 서버 접근 시간 등

<br/>

### **HttpSession 동작**

---

1. 클라이언트에서 서버로 첫번째 요청 전송
2. Web Container는 해당 요청에 session-id가 발급되었는지 확인하고 없다면 설정한다.
3. set-cookie Header에 저장 후 요청처리 뒤 응답한다.
4. 서버 메모리와 Client 스토리지에 저장된다.
5. 클라이언트는 이후 요청부터 저장된 session-id 값을 포함하여 전송한다.
6. Client의 접속 종료 시 세션이 소멸된다. (서버의 Logout 처리, 해당 탭 종료 등)

<br/>

### **HttpSession Method**

---

<br/>

**세션 가져오기**

---

기본적으로 세션이 있다면 해당 세션을 반환하고 없다면 생성하여 반환한다.

```jsx
// Default true , false : 없는 경우 생성하지 않는다. (Null 반환) 
	
	@GetMapping("/hello")
	public ResponseEntity<String> hello(HttpServletRequest request) {
		HttpSession session = request.getSession();

		// 세션이 getSession()을 통해 생성되었는지 여부를 확인한다.
		System.out.println(session.isNew());

		return ResponseEntity.ok("hello");
	}
```

<br/>

**세션 ID 값 가져오기**

---

세션이 생성되었을 때 부여된 식별 값을 가져온다.

```jsx
String id = session.getId(); // 1
```

<br/>

**세션의 생성 여부 확인하기**

---

세션이 생성된 상태인지, 지금 시점에 생성된 것인지 확인할 수 있다.

```java
// 세션이 생성된 상태인지?
if(session == null) {
}

// 세션이 지금 시점에 생성된 것인지?
if(session.isNew()) {
}
```

<br/>

**세션 값 저장**

---

HttpSession은 Map형식의 자료구조를 제공한다.

```jsx
// setAttribute(String name, Object value)
HttpSession session = request.setAttribute("key","value");

// 예시
HttpSession session = request.setAttrubute("id", user.getId());
```

<br/>

**세션 값 조회**

---

저장된 속성의 key 값을 넘겨 value 값을 받아온다.

```jsx
// getAttribute(String name)
// 가져오는 값을 Casting 하여 해당 타입으로 받아올 수 있다.
String value = (String) request.getAttribute("key");
```

<br/>

**세션 값 삭제**

---

저장된 속성의 key 값을 넘겨 value 값을 삭제하거나, 한번에 모든 속성 값을 삭제한다.

```jsx
// removeAttribute(String name)
request.removeAttribute("key");

// 모든 속성 값 삭제
request.invalidate(); 
```

<br/>

**세션 생성, 마지막 접속 시간**

---

해당 세션이 생성된, 마지막에 접속한 시간을 밀리초 단위로 반환한다.

```jsx
long creationTime = session.getCreationTime(); // 1611202337904
long lastAccessedTime = session.getLastAccessedTime(); // 1611202337904
```

<br/>

**세션 활성 시간 설정, 해당 값 가져오기**

---

세션의 활성 시간과 설정된 값을 가져오는 메서드.

```jsx
// setMaxInactiveInterval(int interval)
session.setMaxInactiveInterval(60000);
int maxInactiveInterval = session.getMaxInactiveInterval();
```