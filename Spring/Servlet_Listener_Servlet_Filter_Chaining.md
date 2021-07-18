2021.01.19

### **Servlet Listener**

Serlvet Container에서 발생하는 주요한 이벤트를 감지하며, 특별한 작업이 필요한 경우 사용된다.

**Servlet Context Event**

- Context LifeCycle
- Context Attribute changed
- Resource를 정리할 수 있는 등..

**Session Event**

- Session LifeCycle
- Session Attribute Changed

### Servlet Filter

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F56a5fd3e-d876-4b22-96a4-f46ca19be4aa%2FUntitled.png?table=block&id=9e8387f7-9ebb-4f7c-96fd-ee8da0f0dade&spaceId=3f0ac77b-a821-45cf-b1be-d98cfb99a8c7&width=2960&userId=6c65ec07-595a-45d5-a0cf-68530a8bac2d&cache=v2" width="80%"></img>

Servlet Container와 Servlet 사이의 요청 전달 이전, 이후에 별도의 처리를 할 수 있다.

Filter는 Web.xml에 정의한 순서대로 반영되며. FilterChain 객체의 doFilter()를 통해 연결된다. 

*이를 Filter Chaining이라고 한다.*

<br/>

**web.xml 예시**

```jsx
<web-app>
		
		<filter>
				<filter-name>filter</filter-name>
				<filter-class>...ExamFilter</filter-class>
		</filter>

		<filter-mapping>
				<filter-name>filter</filter-name>
				<servlet-name>servlet<servlet-name>
		</filter-mapping>

		.... servlet 
</web-app>
```

**Filter 예시**

```jsx
public class ExamFilter implements Filter {

		@Override
		public void init(FilterConfig filterConfig) throws ServletException {
				//Do Init
		}

		@Override
		public void doFilter(ServcletRequest request, ServletResponse response, FilterChain chain) throws SevletException {
				//Do Something
				
				// 다음 필터로 이동하거나 Servlet 구현체에게 전달된다.
				chain.doFilter(request, response);
		}
		
		@Override
		public void destroy() {
				//Resource return 
		}
}
```

적**용할 수 있는 것**

- 특정 Servlet 전달 전, 후 요청 처리
- 특정 URI에 대한 요청 처리
- XSS, Injection 같은 보안 공격 방어 (Script 태그, SQL 문법 검증 후 제거 등)
- Login 세션 처리 (체크 등)
- Application 수준의 요청, 응답 Logging