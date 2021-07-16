2020.12.24

</br>

### **RestTemplate?**

Spring 에서 제공하는 HTTP 서비스 호출시 사용할 수 있는 인터페이스이다.

멀티스레드 환경에서 안전하며 여러 기능을 지원한다.

Spring Boot 에서 제공하는 자동 구성 빈 (dataSource, SqlSessionFactory 등의 설정들) 에는 포함되지 않지만, 해당 인터페이스를 인스턴스로 제공하는 RestTemplateBuilder라는 것을  지원해준다.


</br>

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

</br>

### **RestTemplate 를 재정의하기**

사용 목적에 따라서 재정의를 하는 방법은 3가지가 있다고 한다.

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

- **RestTemplateBuilder 빈을 재정의하는 방법**

    구성의 우선 순위로 인하여 자동 구성으로 되는 RestTemplateBuilder 빈을 사용하지 않고  수동으로 재정의한 것을 사용함으로써 변경된 인스턴스를 반환하게 된다.


</br>

### **RestTemplate 에서 제공하는 메서드**

**HTTP DELETE**

- **delete()**

    지정된 URL 에서 특정 Resource 를 삭제한다.

    ```java
    restTemplate.delete("http://example.com/Posts/{postId}", post.getId());

    ```


</br>

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


</br>

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

</br>

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

</br>

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

</br>

**HTTP PUT**

- **put()**

    주어진 요청 객체를 URL으로 PUT 요청을 보내어서 새로운 리소스를 생성한다.

    ```java
    restTemplate.put("http://example.com/Posts/{postId}", 
    		new Post("ChangeTitle", "ChangeContent"), post.getId());
    ```

</br>

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

모든 메서드는 RestClientException 를 던지게 된다.

</br>

## Traverson

</br>

Spring HATEOAS 라이브러리에서 제공하는 하이퍼미디어 API이다.

HTTP Method 를 지원하지 않고, 하이퍼 링크를 이용하여 다른 URL로 이동하게 된다.

기본적으로 Traverson 인스턴스에 API의 기본 URI와 Accept 헤더를 설정할 수 있다.

```java
Traverson traverson = new Traverson(URI.create("http://example.com/")
		, MediaTypes.HAL_JSON);
```

</br>

### Traverson 에서 제공하는 메서드

- **follow**

    Traverson에 Customizing 된 정보로 리소스간의 단일 관계에 대하여 설정한다.

    결과 값의 타입은 Traverson.TraversalBuilder이다.

    ```java
    Traverson.TraversalBuilder builder = traverson.follow("self");

    // 추가적으로 사용할 수 있는 메서드
    // Map<String, ?> 형식의 Key-Value를 전달할 수 있다.
    Traverson.TraversalBuilder builder = traverson.follow("self")
    		.withTemplateParameters(parameters); 

    // 응답에 따른 결과를 지정하는 타입 형식으로 마샬링하는 메서드
    String response = traverson.follow("self").withTemplateParameters(parameters)
    		.toObject(String.class);
    //  .toObject("$.title"); 속성 값 사용 가능
    ```

- **setRestOperations**

    사용할 RestOperations를 구성한다. null이 제공된 경우 RestTemplate를 사용한다.

    ```java
    traverson.setRestOperations(null);
    ```

    **RestOperations?**

    RESTful 작업의 기본적인 작업 단위를 지정한 인터페이스이다.

    주 구현체로는 RestTemplate이 있으며 직접 사용되는 경우는 거의 없지만, 쉽게 모킹하거나 스터빙을 할 수 있음으로써 테스트의 확장성을 향상시키는데 유용하게 사용될 수 있다.

- **setLinkDiscoverers**

    사용할 링크 검색기를 설정한다. 기본적으로 단일 HalLink Discoveryer가 등록된다.

    ```java
    traverson.setLinkDiscoverers(null);
    ```

</br>

## **WebClient**

Spring Flux 라이브러리에서 제공하는 API 호출 인터페이스.  

내부적으로 이벤트 루프 방식을 구현한 Reactive Streams API를 사용한다.

</br>

### **WebClient를 재정의하기**

재정의를 하는 방법은 크게 3가지가 있다고 한다.

- 필요한 순간에 재정의하기

    WebClient.Builder를 주입받아서 메서드를 호출하는 시점에서 생성한다. 해당 변경사항은 해당 애플리케이션에서 생성되는 모든 클라이언트에 반영되게 된다.

    ```java
    private final WebClient webClient;
     
    public MyService(WebClient.Builder webClientBuilder) {
        webClient = webClientBuilder.baseUrl("http://example.com").build();

    		// 혹은

    		webClient = WebClient.builder()
    		    .baseUrl("http://example.com/")
    		    .defaultCookie(cookieKey, cookieValue)
    		    .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE) 
    		    .defaultUriVariables(Collections.singletonMap("url", "http://example.com/"))
        .build();		
    }
     
    public Post someMethod() {
        return webClient.get().url("/posts/{postId}", "1")
    						.retrieve().bodyToMono(Post.class);
    }
    ```

    동일 시점에서 하나의 빌더로 여러 클라이언트를 생성하려는 경우 builder.clone(); 을 통한 빌더   복제를 고려하여야한다.

    ```java
    public MyService(WebClient.Builder webClientBuilder) {
        WebClient.Builder clientBuilder2 = webClientBuilder.clone();
    }
    ```

- **WebClientCustomizer 빈을 통한 커스텀**

- **WebClient 인스턴스 생성방식 이용하기**

    이 방식의 경우 Auto Configuration 이나 WebClientCustomizer가 적용되지 않는다.

    ```java
    WebClient client = WebClient.create();

    WebClient client = WebClient.create("http://example.com/");
    ```

- **추가 :) TCP 커넥션 시 연결 시간에 대한 설정 정보를 커스텀하는 방법 ( TcpClient )**

    ```java
    TcpClient tcpClient = TcpClient
      .create()
      .option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 5000) // 연결 시간 제한을 설정
      .doOnConnected(connection -> {
          connection.addHandlerLast(new ReadTimeoutHandler(5000, TimeUnit.MILLISECONDS));
          connection.addHandlerLast(new WriteTimeoutHandler(5000, TimeUnit.MILLISECONDS));
      });

    WebClient client = WebClient.builder()
      .clientConnector(new ReactorClientHttpConnector(HttpClient.from(tcpClient)))
      .build();
    ```

</br>

### **WebClient 에서 제공하는 메서드**

**Any Method**

- **method(HttpMethod method)**

    ```java
    WebClient webClient = WebClient.create("http://example.com/");

    Post post = webClient.method(HttpMethod.GET)
    		.url("/posts")
    		.retrieve()
    		.bodyToMono(Post.class);
    ```

- **mutate()**

    해당 WebClient 인스턴스의 속성을 변경(정의)하는 빌더를 반환한다.

- **retrieve()**

    앞서 체이닝된 정보대로 HTTP 요청을 수행하고 응답 본문을 가져오게 된다.

- **exchange()**

    응답 상태 및 헤더를 가진 클라이언트의 응답 엔티티을 반환 받는다.

    해당 엔티티에서 응답 본문을 가져올 수 있다.

</br>

**HTTP GET**

- **get()**

    요청을 통해 단일 리소스나 여러건의 리소스를 가져올 수 있다. 

    - **bodyToFlux(Obejct.class) : 여러건의 리소스 반환**
    - **bodyToMono(Object.class) : 단건 리소스 반환**

    ```java
    WebClient webClient = WebClient.create("http://example.com/");

    public Mono<Post> findById(Long id) {
    		return webClient.get().url("/posts/"+ id)
    				.retrieve().bodyToMono(Post.class);
    }

    public Flux<Post> findAll() {
    		return webClient.get()
    				.uri("/posts/")
    				.retrieve()
    				.bodyToFlux(Post.class);
    }
    ```

</br>

**HTTP POST**

- **post()**

    일반적으로 요청을 통해 리소스를 만드는 데 사용된다.

    ```java
    webClient.post()
    		.url("/posts")
    		.body(Mono.just(entity), Post.class)
    		.retrieve()
    		.bodyToMono(Post.class);

    Post savedPost = Mono.just(entity, Post.class);
    ```

</br>

**HTTP DELETE**

- **delete()**

    일반적으로 요청을 통해 리소스를 삭제하는 데 사용된다.

    ```java
    WebClient webClient = WebClient.create("http://example.com/");

    return webClient.delete()
    		.url("/post/" + "1")
    		.retrieve()
    		.bodyToMono(Void.class);
    ```

</br> 

**HTTP HEAD**

- **head()**

    요청을 통해 해당 URI의 지원 헤더를 응답받는데 사용한다.

</br>

**HTTP PUT**

- **put()**

    요청을 통해 하나의 리소스 정보를 업데이트하는 데 사용된다.

    ```java
    WebClient webClient = WebClient.create("http://example.com/");

    return webClient
    		.put()
    		.url("/posts/" + "1")
    		.retrieve()
    		.bodyToMono(Void.class);
    ```

</br>

**HTTP OPTIONS**

- **options()**

    요청을 통해 지원하는 메서드 정보들을 응답받는다.

</br>

**HTTP PATCH**

- **patch()**

    요청을 통해 리소스의 일부분을 업데이트하는 데 사용된다.

    ```java
    WebClient webClient = WebClient.create("http://example.com/");

    Post modifyEntity = webClient
    		.options()
    		.uri("/posts")
    		.retrieve()
    		.bodyToMono(Post.class);
    ```

</br>

## **RestTemplate vs WebClient**

</br>

### RestTemplate

</br>

앞서 말한 것처럼 RestTemplate는 내부적으로 하나의 요청에 하나의 쓰레드를 생성하여 동작시키는 요청당 스레드 모델을 구현한 Java Servlet API를 사용한다. 

각각 스레드가 일정량의 메모리와 CPU주기를 사용하고, 응답을 받을 때까지 스레드가 차단되게 되는데, 이는 요청마다 스레드를 생성하기에 많은 스레드를 생성하여 스레드 풀을 소모하거나 사용 가능한 모든 메모리를 차지할 수 있으며 빈번한 CPU 컨텍스트 (스레드) 전환으로 인해 성능 저하가 발생할 수 있다. **(요청 수에 연관되어 성능 저하가 발생한다.)**

</br>

### WebClient

</br>

각 이벤트에 대해 Task 를 만들고 대기열에 넣은 뒤 적절한 응답이 있는 경우에만 실행하게 된다. 즉 이벤트 루프 방식을 구현한 Reactive Streams API를 사용한다.

→ 응답이 있는 경우에만 실행 스레드가 동작하게 된다.

해당 방식은 RestTemplate에 비해 적은 스레드와 리소스를 사용하면서 동시에 더 많은 로직을 처리 할 수 있다. **(요청 수에 관계없이 일정한 성능을 제공하는 것에 의의를 둔다.)**

</br>

### 선택?

</br>

상황에 따라 다를 수 있지만 들어오는 요청의 수가 많다고 가정하면 WebClient, 비동기 방식을 사용하는 것이 일반적으로 좋은 선택이다. (필요한 만큼만의 스레드, 리소스를 사용하기 때문이다.)

→ 대부분 이런 상황일 수 있기에 WebClient를 권장하는 것 같다.

</br>

### 참고 자료

- Boot String Boot!
- [https://www.baeldung.com/spring-5-webclient](https://www.baeldung.com/spring-5-webclient)
- [https://www.baeldung.com/spring-webclient-resttemplate](https://www.baeldung.com/spring-webclient-resttemplate)
- [https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/html/boot-features-webclient.html](https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/html/boot-features-webclient.html)
- [https://howtodoinjava.com/spring-webflux/webclient-get-post-example/](https://howtodoinjava.com/spring-webflux/webclient-get-post-example/)