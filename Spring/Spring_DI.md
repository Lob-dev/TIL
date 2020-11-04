2020.10.18

<br/>

Spring DI는 스프링이 내부에 있는 Bean 간의 관계를 관리, 주입할 때 사용하는 기법입니다.


Spring DI의 핵심원리는 Container 를 통해 관리되는 Bean이 DI 컨테이너에 의존성이 없어야 한다는 것입니다. 즉, 해당 Bean이 **독립적으로 인스턴스화가 가능한 POJO** 여야 한다는 것인데, 기본적으로 Container 에 관리되는 Bean들은 Container 를 사용하지 않고서도 단위 테스트에서 인스턴스화할 수 있어야 합니다. 


DI를 통해 객체 외부에서 생성된 객체(Bean)를 Interface를 통하여 넘겨받게 되므로 객체간의 결합도를 낮출 수 있고, 런타임시에 의존관계가 결정되기 때문에 유연한 구조를 가질 수 있습니다. 이를 통하여서 컴포넌트화 및 설계를 가능케 하고, 쉽게 Interface 기반의 확장성이 좋은 컴포넌트들을 구현하고 재사용할 수 있습니다.

DI 컨테이너는 기본적으로 Type을 확인하고 Injection 하는 방식인 byType으로 동작합니다. (**@Autowired 그 외에도 Resource** , **Inject**가 존재합니다.**)**

<br/>

**(Automatic Dependency Injection 시 Scan 대상)**

**@Autowired (AutowiredAnnotationBeanPostProcessor)**

우선 빈의 타입을 기준으로 찾아서 주입하는 방식 (**타입 → 이름 → @Qualifier**)

**사용할 수 있는 위치**

- 생성자
- 세터
- 필드


<br/>

**해당 타입의 빈이 여러개인 경우 (모든 경우)**

1. **@Primary**의 유무를 확인한 후 주입한다. (**우선순위 부여**)

    ```java
    @Service @Primary
    public class RealBookService Impliment BookService{
    ```

2. @**Order 기준**으로 확인한 후 주입한다. (**빈들의 우선순위 설정**) 

    ```java
    @Service @Order(1)
    public class RealBookService Impliment BookService{
    ```

3. Bean의 이름**(빈의 ID = Class 명의 Small case)**을 기준으로 주입한다.
4. **@Qualifier 가 설정되어 있는지**를 기준으로 확인한 후 **주입**한다.

    ```java
    @Qualifier("realBookService")

    public BookController (@Qualifier("realBookService")BookService bookService){
    		this.bookService = bookService;
    }
    ```

5. **혹은 모든 빈을 주입받기. (Collection 사용)**

    ```java
    @Autowired
    List<BookService> bookServices;
    ```

6. **필드명을 작성해서 넣기**

    ```java
    @Service
    public class RealBookService Impliment BookService{

    -------------------------------------------

    @Autowired
    BookService realBookServices;
    ```


<br/>

**@Resource (CommonAnnotationBeanPostProcessor)**

우선 빈의 ID (이름) 값을 기준으로 주입하는 방식 **@Resource(name="bookService")**

**사용할 수 있는 위치**

- 필드
- 세터

**해당 타입의 빈이 여러개인 경우** 

1. **@Primary**
2. **@Order**
3. **Bean Type**
4. **@Qualifier**


<br/>

**@Inject (JSR-330's : AutowiredAnnotationBeanPostProcessor)**

**Autowired** 처럼 빈 타입을 기준으로 찾아서 주입하는 방식 (**타입 → @Qualifier → 이름**)

**사용할 수 있는 위치**

- 필드
- 세터
- 생성자
- 메서

**해당 타입의 빈이 여러개인 경우**

1. **@Primary**
2. **@Order**
3. **@Qualifier**
4. **Bean 이름**


해당 어노테이션은 **@Autowired 와 동일하게 작동합니다.**

하지만 자동 주입될 Bean 객체를 찾는 순서가 다릅니다.

**byType → @Qualifier ? → byName  → 실패**

Spring Legacy 환경에서 사용시 maven이나 gradle에 javax 라이브러리 의존성을 추가해야합니다.

```
<dependency>
    <groupId>javax.inject</groupId>
    <artifactId>javax.inject</artifactId>
</dependency>
```

<br/>

### **추가) 의존 객체 선택을 위한 어노테이션**

@Primary = 같은 타입의 설정해야 하는 Bean들이 있을 경우 그중 하나에 설정하면 가장 우선 순위로 주입되게 됩니다.  ex) @Repository @Primary

<br/>

**@Qualifier**

같은 타입을 가진 Bean 들을 Injection 했을 시 원하는 것을 사용하기 위하여 쓰기도 합니다.

appContext.xml에서 이와 같이 선언하고, (부트의 경우 생략가능)

```
 <bean id="gun" class="com.tutorial.spring.Gun">
        <qualifier value="gunner"></qualifier>   <-- Qualifier를 명시한다 -->
    </bean>
```

Autowired와 같이 사용합니다.

```
		@Autowired
    @Qualifier("gunner")   // appContext.xml 에 명시했던 qulifier name을 쓴다
    private Weapon weapon;
```

<br/>

**@Named**

@Named는 xml 컨테이너 설정파일에 다른 정보를 명시할 필요 없이 기존의 bean 의 ID를 가지고 Inject와 같이 사용하기만 하면 됩니다.

```
@Inject
    @Named("gun")  // 스프링 컨테이너 xml 설정파일에 명시되어 있는 ID를 적음
    private Weapon weapon;
```

<br/>

의존 객체 자동 주입의 관련된 내용

출처: [https://engkimbs.tistory.com/683?category=767795](https://engkimbs.tistory.com/683?category=767795) [새로비]

<br/>

### **Bean Injection 방식들**

Field Injection

주입(inject) 대상인 Bean의 생성자를 호출하여 Bean을 Scan 하거나 Bean Factory에 등록한다.          그후에 생성되어있는 Bean의 Autowired 같은 어노테이션이 붙은 필드에 해당하는 Bean을 찾아서  주입하는 방법이다. 그러니까, 먼저 Bean을 생성한 후에 필드에 대해서 주입한다..

```
@Service
public class StudentServiceImpl implements StudentService {

    @Autowired
    private CourseService courseService;

    @Override
    public void studentMethod() {
        courseService.courseMethod();
    }

}
```

<br/>

**Setter Based Injection ( 수정자 주입 )**

주입(inject) 대상인 Bean의 생성자를 호출하여 Bean을 Scan 하거나 Bean Factory에 등록한다.          그 후에 생성자 인자에 사용하는 Bean 을 찾거나 생성하고. 그후에 주입하려는 Bean 객체의 수정자를 호출하여 주입한다.

```
@Service
public class StudentServiceImpl implements StudentService {

    private CourseService courseService;

    @Autowired
    public void setCourseService(CourseService courseService) {
        this.courseService = courseService;
    }

    @Override
    public void studentMethod() {
        courseService.courseMethod();
    }
}
```

<br/>

**Constructor Injection ( Spring 지향,  생성자 주입 )**

Constructor Injection는 처음에 인자에 사용되는 Bean을 찾거나 Bean Factory에서 만들고, 해당 Bean을 통해 주입 대상인 Bean의 Constructor를 호출하여 객체를 생성할 때 인자로 주입한다.

Spring이 생성자 주입을 지향하는 이유는 이 방법을 통해서 순환참조를 미리 예방할 수 있기 때문이다.

```
@Service
public class StudentServiceImpl implements StudentService {

    private final CourseService courseService;
	
		// 빈으로 등록되어 있다면 @Autowired가 생략되어도 자동으로 주입이 된다.
    public StudentServiceImpl(CourseService courseService) {
        this.courseService = courseService;
    }

    @Override
    public void studentMethod() {
        courseService.courseMethod();
    }
}
```

<br/>

### **추가) 생명 주기 관리를 위한 어노테이션**

빈의 생애주기를 관리해준다. ( BeanPostProcessor )

Container 생성 → 빈 생성 → 의존관계 설정 → 빈 초기화 콜백(호출시) → 빈의 사용 →   → Spring Application 종료

```java
@Test
    public void lifeCycleTest() {
        // Container 로드 -> LifeCycleConfig를 통해 해당 빈의 설정 정보를 읽어온다.
				AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
				
				System.out.println("getbean 이전");     
				//getBean을 통해 호출되기 이전에 해당 객체를 초기화 한다.

				NetworkClient client = ac.getBean(NetworkClient.class);
				// 초기화 된 객체를 받아서 사용한다.				

        // AnnotationConfigApplicationContext, ConfigurableApplicationContext 에서 
				// close()를 제공 -> ApplicationContext가 종료된다.
        ac.close();

    }


/*  설정 정보를 통한 생애주기 콜백. (해당 @Bean 설정에서 (initMethod = "init", destroyMethod = "close") 사용)
    public void init(){
        System.out.println("init");
        connect();
        call("초기화");
    }

    public void close(){
        System.out.println("close");
        disconnect();
    }*/

    /*
     어노테이션 설정을 통한 생애주기 콜백.
     최신 스프링에서 권장하는 방법이다. + JSR-250 자바 표준 기술이다. 단점으로는 외부 라이브러리에 적용할 수 없다.
     외부 라이브러리에 적용하려면 설정 정보를 통하여 적용하자. (내부 어노테이션, 외부 Bean 설정)
    */
    @PostConstruct
    public void init(){
        System.out.println("init");
        connect();
        call("초기화");
    }

    @PreDestroy
    public void close(){
        System.out.println("close");
        disconnect();
    }
}
```

<br/>

@PostConstruct : 인스턴스 생성 시 초기 처리를 위한 Method , Void Type

@PreDestroy : 인스턴스 소멸 시 종료 처리를 위한 Method , Void Type

<br/>

### **추가) 인스턴스화와 생존 주기를 정할 수 있는 어노테이션**

@Scope : (“내용”) 을 통해 설정이 가능합니다. Default로 singleton이 설정되어 있으며,                    이용할 때마다 인스턴스화 하는 **Prototype**, Servlet의 Request Scope에서만 생존하는 **request**, Session 에서만 생존하는 **session**, Application Scope 동안만 생존하는 **application**이 있습니다.

출처 : [https://jongmin92.github.io/2018/02/11/Spring/spring-ioc-di/](https://jongmin92.github.io/2018/02/11/Spring/spring-ioc-di/)