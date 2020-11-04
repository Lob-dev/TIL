2020.10.18

<br/>

### **제어의 역전(Inversion of Control, IoC)**

프레임워크의 컨테이너가 객체의 제어권을 가지고 생성부터 생명주기, 관리 그리고 필요한
객체들에게 의존성을 주입해주는 일련의 과정들을 개발자에서 컨테이너로 제어권이
넘어가 일반적인 제어의 흐름이 바뀌었다 하여 제어의 역전이라고 합니다.

<br/>

Spring의 IoC 컨테이너는 EJB 시절의 컨테이너의 단점을 보완하고자 탄생시킨
경량 컨테이너입니다. 이 것은 POJO를 만족하는 객체들을 이용하여 서비스가 가능하도록 지원 해줍니다.

추가적으로 Transaction이나 Object Pooling, 인스턴스 생명주기 관리등의 기능을 지원하여,
테스트의 용이성, 개발 생산성을 향상 시켜줍니다.

해당 사상을 구현하고 사용하는 방식이 Spring의 DI입니다.

<br/>

Spring Bean
  **스프링**에서 **제어권을 가지고 직접 만들어 관계를 부여**하는 **객체 단위의 컴포넌트** 입니다.

  XML, @Component, @Bean, Service, Controller, Repository.. 등으로 등록하게 됩니다.

<br/>

### BeanDefinition

빈 설정 메타정보로써 쉽게 말하면 역활과 구현을 개념적으로 나눈 것.

Java(Annotation Based) - @Bean , XML - <bean> 를 읽어서 BeanDefinition을 만듭니다.

Spring Container는 이 메타정보를 기반으로 Spring Bean을 생성합니다.

<br/>

**Ex) Annotation Based**

**@Bean → BeanDefinition → XXXBeanDefinitionReader → XXXApplicationContext** 

<br/>

### **Bean Factory**

스프링의 IoC를 담당하는 핵심 컨테이너로써 bean을 등록, 생성, 조회, 반환, 관리 (생명주기)합니다
보통 이를 확장한 ApplicationContext나 이것을 확장한 WebApplicationContext를 이용합니다.

<br/>

### **ApplicationContext**

Bean factory를 확장한 IoC 컨테이너로 핵심 컨테이너의 기능과 추가적으로
Spring의 각종 부가 서비스를 제공합니다.
ApplicationContext는 구현하여야 하는 Interface이고 Bean Factory를 상속받습니다.

제공하는 부가 서비스로는 

**MessageSource : 국가마다 로컬라이징을 함으로서 쉽게 각 지역에 맞춘 메세지를 제공**

**EnvironmentCapable : profile과 property, 환경변수(로컬, 개발, 운영 구분 처리) 기능 제공**

**ApplicationEventPublisher : 애플리케이션 이벤트에 필요한 인터페이스를 제공 (발행, 구독..)**

**ResourceLoader : 파일, 클래스패스, 외부 등에서 리소스를 읽어오는 기능을 제공** 

 을 등이 있습니다. (상속)

 <br/>

**ApplicationContext의 구현체**

AnnotationConfigApplicationContext, GenericXmlApplicationContext ..... 

<br/>


### **WebApplicationContext**

Spring의 ApplicationContext를 확장한 인터페이스로, 웹 애플리케이션(MVC)에서 사용할 수 있는   메서드인 getServletContext라는 것이 추가되었고, 이것을 통해 ServletContext를 얻을 수 있습니다. 추가적으로 SevletContext를 이용하는 (Applicaion, request, session 등) 생애 주기 스코프도 사용할 수 있습니다.

<br/>

### **Bean Scanner**

지정된 패키지 하위에 있는 모든 패키지들을 대상으로 필터를 적용하여 빈을 등록하는데,               이 필터에 걸리는 어노테이션을 Stereotype 어노테이션이라고 합니다.
Stereotype 어노테이션이란 @Component, @Service 등을 말하는 것입니다.
Stereotype 어노테이션을 적용할 때 특정 계층으로 확정짓기 어려운 클래스들은
@Component를 적용하는 것이 바람직 합니다.
@Service, Repository, Controller 등은 Component의 확장형 입니다.
@Bean 은 제어가 불가능한 외부 라이브러리 등을 Bean으로 만들 때 사용합니다.
@Configuration 은 Bean 정의를 실행하기 위한 JavaConfig 용 어노테이션입니다.

<br/>

### **Configuration Metadata**

Application context나 Bean factory 같은 IoC 컨테이너가 IoC 를 적용하기 위해 사용하는 메타 정보,
주로 Bean을 생성, 구성하는 용도로 사용됩니다

<br/>

### **IoC Container**

Bean Factory나 Application Context를 가리키는 용어 입니다.
ApplicationContext 객체는 보통 여러개가 만들어지며 이것들을 통칭해서
Spring Container라고 부르기도 합니다.