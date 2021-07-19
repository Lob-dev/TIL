2021.01.29

<br/>

### **Bean? Component?**

<br/>

### **@Component (암시적인, 자동 구성)**

<br/>

**Class path Scan을 통해 Definition들을 읽어오고, 그것들을 사용하여 구성한다.**

@Configuration Annotation과 같이 사용될 필요가 없고, Class Level에 설정된다.

해당 Annotation을 확장한 하위 Annotation들이 존재한다. 

Component에서 Bean을 사용한다면 해당 객체가 컨테이너에 의해 관리되는 것이 아니라 내부적으로 호출되어 사용하는 팩토리 메서드가 된다. 

<br/>

*해당 스택 영역에 가시적인 메서드 호출*

<br/>
<br/>

### **@Bean (명시적인 구성)**

<br/>

**단일 Definition을 명시적으로 선언하고 사용한다.**

@Configuration Annotation과 같이 사용되어야하고, Method Level에서 설정된다.

Spring 환경에서 동작하지 않거나, Component가 붙어있지 않는 외부 API를 @Bean 주석을 통해 포장할 수 있으며, 이를 통해 Singleton 객체로써 생성, 사용할 수 있다. 

<br/>

*(ApplicationContext에 Load하기에 DL, DI도 가능해진다.)*