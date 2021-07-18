2021.01.20

### **Interceptor**

컨트롤러 요청 이전이나 요청 처리 후 클라이언트로 응답하기 전에 적용할 수 있는 인터페이스이다. Servlet Filter와 유사하고, Servlet Chaining과 같이 Interceptor Chain을 구성하며, 각각의 요소들은 HandlerMapping Bean으로 정의된다. 

Interceptor는 ApplicationContext에 의해 Bean definition 정의가 읽어지고 참조된다.

<br/>

HandlerMapping Bean은 DispatcherSerlvet 이 요청 처리를 위해 메서드를 매핑할 때 호출하며, HandlerAdpater를 통해 매핑된 메서드를 호출하게 된다. 이때 인터셉터 정보가 같이 넘어가 컨트롤러 요청 이전이나 이후에 호출되게 된다. 

*타겟 메서드 호출 이전, 이후, 랜더링 메서드 호출 이후에 실행*

<br/>

**해당 인터페이스 메서드**

- preHandle() : 타겟 메서드가 호출되기 전에 호출
- postHandle() : 타겟 메서드가 실행된 후 호출 (HandlerAdapter 처리 후, 뷰 생성 이전)
- afterCompletion() : View가 랜더링된 뒤에 실행된다

```java
@Override 
public void afterCompletion( HttpServletRequest request, HttpServletResponse response, 
Object handler, Exception ex) throws Exception { 

final ContentCachingRequestWrapper cachingRequest = (ContentCachingRequestWrapper) request; 
final ContentCachingResponseWrapper cachingResponse = (ContentCachingResponseWrapper) response; 

String reqContent = new String(cachingRequest.getContentAsByteArray(), StandardCharsets.UTF_8);
String resContent = new String(cachingResponse.getContentAsByteArray(), StandardCharsets.UTF_8);

}

```