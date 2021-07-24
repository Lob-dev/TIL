2021.02.02

<br/>

### Filter 구현하기

---

```java
public class XxxHandleFilter implements Filter {

		@Override
    public void destroy() {
        // ...
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // ...
    }

    @Override
    public void doFilter(ServletRequest request, 
               ServletResponse response, FilterChain chain)
        throws IOException, ServletException {

        chain.doFilter(request, response);
        
    }

}
```

<br/>

### 특정 요청에만 Filter 등록하기 *@Bean*

---

<br/>

**Bean 방식**

```java
@Bean
public FilterRegistrationBean<XxxHandleFilter> Filters() {
		FilterRegistrationBean<XxxHandleFilter> registration = new FilterRegistrationBean<>();
		
		registration.setFilter(new XxxHandleFilter());
		registration**.**addUrlPatterns("URL Path"); // "api/users/*"
		// registration.setUrlPatterns("URL Path");

		return registration;
}
```

<br/>

**자동 구성 방식**

```java
@ServletComponentScan
@SpringBootApplication
public class MainApplication {
		public static void main(String[] args) {
				SpringApplication.run(Method2Application.class, args);
		}
}

@WebFilter(urlPatterns = "URL Path") // "api/users/*"
public class MyFilter implements Filter {
		// 필터 구현
}
```

<br/>

### 모든 요청에 Filter 적용하기 *@Component*

---

```java
// 모든 요청에 적용되는 Filter 형식을 구현
@Component
public class MyFilter implements Filter {
		// 필터 구현
}
```

Filter Chain의 처리 순서를 적용하기 위해선 @Order Annotation을 사용하면 된다.

@Bean (FilterRegistrationBean),  @ServletComponentScan + @WebFilter , @Component 중 하나의 방식만 지정하여 사용해야한다. *Filter가 중복 등록된다.*