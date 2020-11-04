2020.10.21

<br/>

**@ComponentScan**

Spring 3.1 버전에 도입되었다.

**스캔할 패키지**와 **Stereotype annotations** 에 대한 정보를 가지고 있다.

해당 어노테이션이 붙은 클래스에서 Filter 설정 값을 적용한 상태로 동등한 패키지와 하위   패키지를 모두 스캔하게 된다.

실제 스캔은 **ConfigurationClassPostProcessor**, **BeanFactoryPostProcessor**에 의해 실행된다

<br/>

**Stereotype annotations ?**

**@Component (최상위 annotations  = 각각의 빈을 등록하기 위해 사용)**

**@Controller  ( Spring MVC 라이브러리가 있는 경우 )**

**@Service**

**@Repository** 

**@Configuration** 

**Spring이 지원해주는 기능을 통하여 Annotation을 상속하듯이 사용할 수 있다.**

<br/>

**Stereotype annotation** 이 붙어있다면 부가 기능을 수행해준다.

**@Controller  = MVC 컨트롤러로 인식**

**@Service       = 개발자가 비즈니스 계층으로 인지하게 함 (부가 기능 X)**

**@Repository = 데이터 접근 계층으로 인식, DB 예외를 스프링 예외로 추상화하여 반환**

**@Configuration = 스프링 설정 정보로 인식하고 싱글톤을 유지하도록 추가 처리를 해준다.**

Singleton Bean들은 Application 실행시 모두 등록되기에 해당 Scope의 Bean이 많다면,              성능상 좀 느릴 수 있다. 

<br/>

Functional을 사용한 Bean 등록 - Spring 5.0 도입

CGI API (Proxy) 나 Reflection API 를 사용하지 않고 Bean을 생성하기에 성능상 이점이 존재한다. (Application 구동 타임상 성능을 이야기한다.)

```java
// 인스턴스를 통한 Spring 실행  ~~ JDK 7
var app = new SpringApplication(SpringApplication.class);
app.addInitializers(new ApplicationContextInitalizer<GenericApplicationContext>()){
		@Override
		public void initalize(GenericApplicationContext applicationContext){
				ctx.registerBean(UserService.class); // 패키지 밖 Configuration or Bean 등록
		}	

//OR Lamdba  JDK 8 ~~
var app = new SpringApplication(SpringApplication.class);
app.addInitializers((ApplicationContextInitalizer<GenericApplicationContext>) ctx -> )
			ctx.registerBean(UserService.class)
}
app.run(args);

```