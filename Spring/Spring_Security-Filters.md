2020.11.03

<br/>


## Spring Security 란?

스프링 시큐리티는 인증 (authentication), 인가 (authorization), 주요 취약점 공격 방어를 종합적으로 지원하고, 스프링의 특성상 다른 라이브러리와의 통합도 지원하므로 사용성을 단순화해 준다.

<br/>

### Authentication (: 인증)

인증이란 특정 리소스에 접근하려고 하는 사용자가 누구인지 확인하는 절차를 말한다.

보통 이름과 비밀번호를 통해서 사용자임을 인증하며, 식별된 이후에는 그에 따른 권한을 부여한다.

<br/>

### Authorization (: 허가, 권한)

인가란 사용자가 지니고 있는 권한의 수준이기도 하며, 해당 권한을 확인하는 절차일 수도 있다.

시스템에서는 특정 리소스에 접근할 때 사용자가 그에 맞는 권한을 지니고 있는지 확인하여야 한다.

<br/>

## Servlet 기반 Spring Security

### **Filter Chain**

Spring Security는 서블릿 Filter 를 기반으로 기능을 지원하게 되어있다.

<img src="./../image/filterchain.png" width="50%"></img>

클라이언트는 어플리케이션으로 요청을 전송하고, 컨테이너는 Servlet과 여러 Filter로 구성된 FilterChain을 만들어 요청 URI 을 기반으로 HttpServletRequest를 처리한다.

- 최초 요청 시에는 HttpServletRequest, Response 를 생성하는 절차가 따로 진행된다.
- 각각의 Request, Response 를 하나의 Servlet이 담당한다.

> **Spring 에서의 Servlet은 DispatcherServlet 이다.**

<br/>

Filter는 각 요청마다 여러개를 적용할 수 있으며, Filter Chain 에 연결되어야 적용이 가능하다.

- HttpServletResponse 를 통해 진행되던 요청이 Filter의 조건에 맞을 경우, 다음 Filter나 Servlet 에게 넘기지 않고 반환할 수 있다.
- Filter Chain 내부에 존재하는 Filter와 Servlet으로 DownStream의 진행 됨을 통해  HttpServletRequest나 HttpServletResponse의 내용을 수정할 수 있다.
    - 요청 Wrapping 을 통한 Content, Header의 추가 등

Filter는 앞서 말했듯이 DownStream 방식으로 Chain에 걸린 것들이 적용됨으로 순서가 매우 중요하다.

<br/>

### DelegatingFilterProxy

Spring Boot 는 Filter의 구현체인 DelegatingFilterProxy 를 통하여서 Servlet Container의 Life Cycle 과 Spring 의 ApplicationContext를 연결한다.

<img src="./../image/delegatingfilterproxy.png" width="50%"></img>

기존에 Servlet Container는 자체적인 표준을 준수하는 Filter 가 아니라면 다른 것들, 즉 Spring 에서 정의한 Filter, Interceptor 를 구현한 Bean 을 인식하지 못하는데, Spring 은 **DelegatingFilterProxy** 를 통하여서 Servlet Container의 표준 메커니즘으로 등록하면서도 처리를 Spring 내부에 Filter를 구현한 Bean 으로 처리를 위임할 수 있다.

```jsx
public void doFilter(ServletRequest request, ServletResponse response, 
																																		FilterChain chain) {
    // Lazily get Filter that was registered as a Spring Bean
    // For the example in DelegatingFilterProxy delegate is an instance of Bean Filter0
    Filter delegate = getFilterBean(someBeanName);

    // delegate work to the Spring Bean
    delegate.doFilter(request, response);
}
```

**DelegatingFilterProxy**  를 통해서 Filter 를 구현한 Bean을 Lazy 하게 참조할 수 있다. 

- 최초 요청이 들어온 후 필요하게 되었을 때, Bean이 생성되게끔 할 수 있다.
- 그 이후에는 ContextLoaderListener를 통해서 Bean을 로드하게 된다.

<br/>

### FilterChainProxy

Spring Security 는 자체적으로 제공하는 특별한 Bean인 **FilterChainProxy** 이라는 것으로 Servlet을 지원한다. SecurityFilterChain 를 통해 여러 Filter Bean 들에게 위임할 수 있으며, **DelegatingFilterProxy**  를 통해 Wrapping 되어 있다.

<img src="./../image/filterchainproxy.png" width="50%"></img>

<br/>

### SecurityFilterChain

**FilterChainProxy** 는 요청에 대해서 Spring Security Filter 를 적용시켜야 할 때 **SecurityFilterChain** 에게 전달하게 된다. **SecurityFilterChain** 는 내부적으로 Filter Chain과 동일한 구조와 **DownStream** 방식으로 구현되어 있다.

<img src="./../image/securityfilterchain.png" width="50%"></img>

**FilterChainProxy** 를 통해서 **SecurityFilterChain** 에게 요청을 위임하게 될 경우 Servlet 처리에 대한 일종의 End Point가 되어서 Debug 를 하기에 좋은 시작점이 된다.

그외에도 **FilterChainProxy** 은

- **SecurityContext**를 비워 메모리 릭을 방지할 수 있고
- **HttpFirewall**을 적용해서 특정 공격 유형을 방어할 수도 있다.
- 추가적으로 **SecurityFilterChain** 를 어떨 때 실행해야 할지도 좀 더 유연하게 결정할 수 있다.
    - **Servlet Container는** URL 만을 사용하여 실행할 Filter를 선택하지만 **FilterChainProxy** 는 **RequestMatcher** 인터페이스를 사용하면 **HttpServletRequest**에 있는 모든 요소로 실행 여부를 결정할 수 있다.
    - 사용할 **SecurityFilterChain** 를 선택할 수도 있다.

        <img src="./../image/multi-securityfilterchain.png" width="50%"></img>

        **먼저 매칭되는 SecurityFilterChain 만을 실행할 수 있다. (DownStream!)** 

        **특정한 처리를 앞에, 공통 처리를 뒤에 두는 것이 올바르게 작동하게끔 하는 방법이다.**

        **SecurityFilterChain** 에는 0개부터 N개 만큼의 Filter를 선언하는 것이 가능하다.

        - 0개의 경우 어떠한 요청에 대해선 Spring Security가 무시하게끔 처리하는 것이다.

<br/>

### Security Filters

Security Filters 는 SecurityFilterChain API를 사용해서 FilterChainProxy에 추가한다. 이땐 Filter의 순서가 중요하다.

- **SecurityFilterChain API 구현체에 대한 순서**

<br/>

### Handling Security Exceptions

ExceptionTranslationFilter는 AccessDeniedException을 해석하고 AuthenticationException을 HTTP 응답으로 바꿔준다.

어플리케이션에서 AccessDeniedException이나 AuthenticationException을 던지지 않으면 ExceptionTranslationFilter는 아무 일도 하지 않는다.

ExceptionTranslationFilter는 FilterChainProxy에 하나의 보안 필터로 추가된다.

<img src="./../image/exceptiontranslationfilter.png" width="50%"></img>

- (1) 먼저 ExceptionTranslationFilter는 FilterChain.doFilter(request, response)를 호출해서 어플리케이션의 나머지 로직을 실행한다.
- (2) 인증받지 않은 사용자였거나 AuthenticationException이 발생했다면, *인증을 시작한다*.
    - [SecurityContextHolder](https://godekdls.github.io/Spring%20Security/authentication#101-securitycontextholder)를 비운다.
    - [RequestCache](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/web/savedrequest/RequestCache.html)에 HttpServletRequest를 저장한다. 사용자 인증에 성공하면 RequestCache로 기존 요청 처리를 이어간다.
    - AuthenticationEntryPoint는 클라이언트에 credential을 요청할 때 사용한다. 예를 들어 로그인 페이지로 리다이렉트하거나 WWW-Authenticate 헤더를 전송한다.
- (3) 반대로 AccessDeniedException이었다면, *접근을 거부한다*. 거절된 요청은 AccessDeniedHandler에서 처리한다.

```jsx
try {
    filterChain.doFilter(request, response); // (1)
} catch (AccessDeniedException | AuthenticationException e) {
    if (!authenticated || e instanceof AuthenticationException) {
        startAuthentication(); // (2)
    } else {
        accessDenied(); // (3)
    }
}
```