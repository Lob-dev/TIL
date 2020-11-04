2020.10.28

<br/>

### Spring AOP

**비즈니스 로직을 제외한 인프라 로직을 따로 모듈화하여 Proxy 기반으로 적용하는 것.** 

<br/>

**인프라 로직?**

- Application 전 영역에 나타날 수 있는 로직.
- 중복 코드가 발생할 수 있는 로직.
- **비즈니스 로직이 아닌 것.**
- **성능 검사, 로거-로깅, 인증, 인가, 트랜잭션 등을 말한다.**

하나의 **인프라 로직을 관점의 분리를 통해 모듈화**하게 되고

- **재사용성의 극대화**
- **효율적인 유지보수**
- **로직의 변화에 대한 유연적인 대처 - 인프라 로직도 캡슐화하는 것!**

해당 요소들을 만족시킬 수 있다.

<br/>

**Spring AOP의 특징?**

- **프록시 패턴 기반의 AOP 구현체이다. (CGLIB을 사용하여 Class Proxy도 가능)**
- Spring Container에 존재하는 **Bean에만 적용 가능하다.**
- 모든 AOP의 기능을 제공하기 보단, **중복 코드의 제거와 Proxy 작성에 대한 번거로움,     객체 간의 복잡한 관계, 로직 들을 해결하기 위함이다.**

<br/>

**AOP의 주요 개념**

- **Aspect : 인프라 로직을 모듈화 한 것이다.**
  
 ```java
@Aspect //Aspect의 역할을 하는 클래스로 지정
public class somethingAop {
	//do something..
	@Pointcut("within(com.lob.*)")
	private void pointcutMethod() {
		//do something
	}
```

- **Target** : 인프라 로직(모듈)을 **적용할 대상이다.** (Class, Method)
- **Advice** : 언제 인프라 로직을 적용할지에 대한 부가 기능이다.
    - @Around : 어드바이스가 타겟 메소드를 감싸서 타겟 메소드 호출전과 후에 어드바이스 기능을 수행

    - @Before : 어드바이스 타겟 메소드가 호출되기 전에 어드바이스 기능을 수행

    - @After : 타겟 메소드의 결과에 관계없이( 성공, 예외 관계없이 ) Advice 수행

    - @AfterReturning : 정상 반환 후) 타겟 메소드가 성공적으로 결과값을 반환 후에 Advice 수행

    - @AfterThrowing : 예외 발생 후) 타겟 메소드가 예외를 던지게 되면 Advice 수행
    - @PerLogging : 경로 지정 방식이 아닌 어노테이션 방식으로 해당 어노테이션이 붙은 메서드만 실행
    - ```java
      @Around("@annotation(PerLogging)")
      //Do something 

          @PerLogging
          @Override
          public void createEvent() {
              //Do something 
          }

          @Override
          public void publishEvent() {
              //Do something 
          }

          @PerLogging
          @Override
          public void deleteEvent() {
              //Do something 
      }
      ```
- **JoinPoint** : **Advice가 적용될 위치**, Method 진입 지점, 생성자 호출 시점, 객체,

    필드에서 값을 꺼낼 때 등 **적용 가능한 다양한 상황을 말한다.**

- **PointCut : 실제 Advice가 적용될 메서드(지점)를 설정한다.**

<br/>

**어노테이션 실행 순서**

@Before -> @Around -> @After

                       @AfterReturning (정상 반환) OR 

                       @AfterThrowing (예외 반환)

<br/>

**AOP Weaving** 

**PointCut 에 Advice 를 적용하는 코드 수정 방식을 말한다.**

- **Compile-Time Weaving : AspectJ**
    - 소스코드를 받아 바이트코드 변환 시에 Aspect를 적용한다. ( .java → .class )
    - Lombok 과 같은 **Compile 시 간섭하는 라이브러리와 충돌**이 일어난다고 한다.
- **Binary Weaving = Post-Compile Weaving : AspectJ**
    - JAR 를 이용하여 Weaving 하는 경우. 컴파일된 바이너리 클래스에 적용한다.
- **Class-Load Time Weaving : AspectJ**
    - Class Loader가 클래스를 로딩할 때 Webving한다.
    - JVM에서 제공하는 agent 를 통해서 기능을 지원받아 적용한다.
- **Runtime Weaving : JDK, CGLIB Dynamic Proxy : Spring AOP**
    - ApplicationContext에 로드되는 객체를 Proxy 로 감싸 Aspect를 적용한다.
    - Spring 내부의 Bean을 살펴보면 CGLIB 이라는 것이 붙어 생성된 것을 알 수 있다.

AspectJ 는 개발자가 만든 AOP 코드 와 Business Code의 bytecode를 Weaving 해서 AOP를 구현 할 수 있도록 해준다