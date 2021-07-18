2021.01.18

**QueryString**

```jsx
// API POINT
@PatchMapping("/users/{userId}/password")
public ResponseEntity<String> modifyPassword(
		@RequestParam(value = "password") String setPassword, 
		@PathVariable Long userId) {
		/...
}

// MOCK TEST : QueryString 전달
{
mockMvc.perform(patch("/users/{userId}/password?password=testPassword", 1L)
			.contentType(MediaType.APPLICATION_JSON))
			.andDo(print())
			.andExpect(status().isOk());

//or

mockMvc.perform(patch("/users/{userId}/password", 1L)
			.contentType(MediaType.APPLICATION_JSON))
			.param("password", "testPassword")
			.andDo(print())
			.andExpect(status().isOk());
}

// REQUEST
// URL에 일부로 작성되어 전송된다.

PATCH /users/{userId}/password?password=testPassword HTTP/1.1

```

- 클라이언트와 API 사이의 전송 흐름에서 모든 사람이 URL 정보를 읽을 수 있다.
    - 암호, 개인 정보와 같은 민감한 데이터는 쿼리 문자열에 넣어서는 안된다.

<br/>

**RequestBody**

```jsx
POST /api/users HTTP/1.1
Content-Length: 68
Content-Type: application/json
Host: localhost:8080

{
  "name" : "user",
  "email" : "hello@kakao.com",
  "age" : "24"
}
```

- 물론 해당 내용도.. HTTP로 전송될 경우 패킷을 가로체면 접근할 수 있다.
    - 최소한 URI에 보이지는 않으니..