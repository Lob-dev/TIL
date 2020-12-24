2020.12.24

<br/>

### **RestTemplate?**

Spring 에서 제공하는 HTTP 서비스 호출시 사용할 수 있는 인터페이스이다.

멀티스레드 환경에서 안전하며 여러 기능을 지원한다.

Spring Boot 에서 제공하는 자동 구성 빈 (dataSource, SqlSessionFactory 등의 설정들) 에는 포함되지 않지만, 해당 인터페이스를 인스턴스로 제공하는 RestTemplateBuilder라는 것을  지원해준다.

<br/>


**기본 사용 방식**

```java
@Component
public class ApiConfiguration{

		private final RestTemplate restTemplate;

		@Autowired
		public RestExample(RestTemplateBuilder builder){
				restTemplate = builder.build(); // 아무 설정없는 RestTemplate 설정
		}

		// returnClassType exam : String.class , User.class.... 
		public String findOne() {
				return restTemplate.getForObject(apiUrlPath, returnClassType))
		}
}
```

<br/>


### **RestTemplate 를 재정의하기**

사용 목적에 따라서 재정의를 하는 방법은 3가지가 있다고 한다.

<br/>

- **필요한 순간에 재정의하는 방법**

    자동 구성된 RestTemplateBuilder 빈을 주입받아 메서드가 호출되는 시점에 생성하는   방법이다. 이때 빌더를 이용해 추가적인 기능을 정의하여 새로운 인스턴스로 반환한다.

    ```java
    private final RestTemplate restTemplate;
     
    public MyService(RestTemplateBuilder restTemplateBuilder) {
        this.restTemplate = restTemplateBuilder.build();
    }
     
    public Post someMethod() {
        return restTemplate.getForObject("/{name}/details", Post.class, name);
    }
    ```

<br/>


- **애플리케이션 전체에 적용되도록 하는 방법**

    RestTemplateCustomizer 라는 빈을 재정의하여 추가하는 방법이다. RestTemplateBuilder 에도 변경된 내용이 반영되므로 인스턴스를 생성하여 반환하면 된다. 

    ```java
    public class CustomRestTemplateCustomizer implements RestTemplateCustomizer {
        
    		@Override
        public void customize(RestTemplate restTemplate) {
    		    // Do someThing
    		}
    }

    @Bean
    public CustomRestTemplateCustomizer customRestTemplateCustomizer() {
        return new CustomRestTemplateCustomizer();
    }
    ```

<br/>


- **RestTemplateBuilder 빈을 재정의하는 방법**

    구성의 우선 순위로 인하여 자동 구성으로 되는 RestTemplateBuilder 빈을 사용하지 않고  수동으로 재정의한 것을 사용함으로써 변경된 인스턴스를 반환하게 된다.

<br/>


### **RestTemplate 에서 제공하는 메서드**

<br/>


**HTTP DELETE**

- **delete()**

    지정된 URL 에서 특정 Resource 를 삭제한다.

    ```java
    restTemplate.delete("http://example.com/Posts/{postId}", post.getId());

    ```

**HTTP GET**

- **getForObject()**

    지정된 URL 에 대한 GET을 수행하고 응답이 있는 경우에 해당 Resource를 반환한다.

    ```java
    // URL에 제공될 PathVarialbe에 대하여서 값을 전달할 수 있다.
    String result = restTemplate.getForObject(
    		"http://example.com/Location/{locationId}/Posts/{postId}", String.class
    		, "42", "21");
    ```

- **getForEntity()**

    지정된 URL 에 대한 GET을 수행하여 ResponseEntity를 반환받는다.

    ```java
    ResponseEntity<String> response = restTemplate.getForEntity(
    		"http://example.com/Posts/{postId}", String.class, "1");
    ```

**HTTP HEAD**

- **headForHeders()**

    URL로 지정된 리소스의 모든 헤더를 검색하고 반환한다. (HttpHeaders Type)

    ```java
    HttpHeaders httpHeaders = restTemplate.headForHeaders(url);
    // httpHeaders.getContent()...
    ```

    **HttpHeaders?**

    HTTP 요청이나 응답에 포함된 헤더를 나타내는 일종의 데이터 구조(객체)이다.

    문자열 헤더 이름을 문자열 값 목록에 매핑하고 유형에 따른 접근자를 제공한다.

    - getFirst(String) : 주어진 헤더 이름과 관련된 첫 번째 값을 반환한다.
    - add(String, String) : 주어진 헤더 이름과 그에 따른 값을 추가한다. (Map 형식)
    - set(String, String) : 특정 헤더가 가진 값을 해당 문자열 값으로 변경한다.

        → 그외에도 containsKey, addAll, equals, getAccept 등의 편의 메서드를 제공한다.

**HTTP OPTIONS**

- **optionsForAllow()**

    지정된 URL 에 대한 Allow 헤더의 값을 반환한다.

    ```java
    Set<HttpMethod> allowedMethod = template.optionsForAllow(
    		new URI("http://example.com/Posts" + "/get"));
    ```

    **Allow Header?**

    해당 리소스가 지원하는 메서드의 집합을 나열해주는 헤더이다.

    서버에서 405 Method Not Allowed 를 응답할 경우 해당 헤더를 무조건 보내야한다.

    만약 해당 헤더가 비어있다면 어떠한 요청 메서드이든 간에 허용되지 않음을 의미한다.

**HTTP POST**

- **postForLocation()**

    주어진 **HttpEntity** 를 URL에 요청하여 리소스를 만들고 Location 헤더 값을 반환받는다.

    해당 결과 값은 일반적으로 리소스가 저장된 위치(URI)를 제공한다.

    ```java
    HttpEntity<String> requestEntity = new HttpEntity<>("Lob!");
    URI location = restTemplate.postForLocation(url, request);
    ```

    **HttpEntity?**

    헤더 및 본문으로 구성된 HTTP 요청, 응답 엔티티를 나타내는 인터페이스이다.

    - **RequestEntity, ResponseEntity 의 상위 클래스이다. (구현체 들)**

- **postForObject()**

    주어진 객체를 URL에 요청하여 리소스를 만들고 응답을 해당 리소스로 받는다.

    ```java
    restTemplate.postForObject("http://example.com/Posts", post, Post.class);
    ```

- **postForEntity()**

    주어진 객체를 URL에 요청하여 리소스를 만들고 응답을 ResponseEntity로 받는다.

    ```java
    ResponseEntity<Post> responsePost = restTemplate.postForEntity(
    		"http://example.com/Posts", post, Post.class)
    ```

**HTTP PUT**

- **put()**

    주어진 요청 객체를 URL으로 PUT 요청을 보내어서 새로운 리소스를 생성한다.

    ```java
    restTemplate.put("http://example.com/Posts/{postId}", 
    		new Post("ChangeTitle", "ChangeContent"), post.getId());
    ```

<br/>


**Any Method**

- **exchange()**

    지정된 URI 템플릿에 대하여 HTTP 메서드를 실행한다.

    RequestEntity를 사용하여 요청하며 반환 값은 ResponseEntity로 제공받는다.

    ```java
    HttpHeaders requestHeaders = new HttpHeaders();
    requestHeaders.add("Accept", MediaType.APPLICATION_JSON_VALUE);

    HttpEntity<String> requestEntity = new HttpEntity<>(requestHeaders);

    ResponseEntity<Post> = restTemplate.exchange(
    		"http://example.com/Posts/{postId}", HttpMethod.GET, 
    		requestEntity, Post.class, "10");
    ```

- **execute()**

    지정된 URI 템플릿에 대하여 HTTP 메서드를 실행한다.

    Request Callback으로 요청을 받고, Response Extractor로 응답을 읽는다.

    ```java
    Post request = new Post("title","content");
    RequestCallback requestCallback = httpEntityCallback(request);
    execute(url, HttpMethod.PUT, requestCallback, null)
    ```

<br/>

모든 메서드는 RestClientException 를 던지게 된다.