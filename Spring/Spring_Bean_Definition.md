2020.10.18

<br/>

Spring Bean Definition 주요 속성

- **class**
    정규화된 자바 클래스 이름

- **id**
    bean의 고유 식별자

- **scope**
    객체의 범위 (sigleton, prototype)

- **constructor-arg**
    생성 시 생성자에 전달할 인수

- **property**
    생성 시 bean setter에 전달할 인수

- **init method와 destroy method (Life Cycle Method)**

<br/>

**XML을 통한 Bean 설정**

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

		// constructor-arg -> 해당 빈의 생성자 주입을 의미
		// property 를 통하여서 주입할 수도 있다. (별 다른게 없어보인다..)
    <bean id="memberService" class="com.hello.core.member.MemberServiceImpl" >
        <constructor-arg name="memberRepository" ref="memberRepository" />
		 // <property name="memberRepository" ref="memberRepository" />
    </bean>

    <bean id="memberRepository" class="com.hello.core.member.MemoryMemberRepository" />

    <bean id="orderService" class="com.hello.core.order.OrderServiceImpl" >
        <constructor-arg name="memberRepository" ref="memberRepository" />
        <constructor-arg name="discountPolicy" ref="discountPolicy" />
    </bean>

    <bean id="discountPolicy" class="com.hello.core.discount.RateDiscountPolicy"/>

		// 위의 방식을 사용하는 것은 매우 번거롭고 복잡해진다.
		// xml 에서도 component scan이 가능하다
		
		// stereo Annotation을 이용하여 스캔한다. (@Component, Controller, Service, Repository)
		// <context:component-scan base-package="com.hello.core"/>
		
</beans>
```

<br/>

**Java Configuration 를 통한 Bean 설정**

```java
//해당 Annotation을 통해 Bean 설정 파일임을 알림.
@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemoryMemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    @Bean
    public DiscountPolicy discountPolicy() {
        return new FixDiscountPolicy();
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(
                memberRepository(),
                discountPolicy());
    }
}
```

<br/>

Annotation 기반의 Component Scan을 이용한 Bean 설정

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })

public @interface SpringBootApplication {

----------------------------------------------------------

@SpringBootApplication // component Scan이 추가되어 있다.
public class SpringCoreApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringCoreApplication.class, args);

    }
}

OR

@ComponentScan(
		excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION 
																								,classes = Configuration.class)
//basePackages 를 주지 않는다면 기본적으로 자신의 위치를 기준으로 하위 패키지까지 
//스캔한다. 모든 빈을 스캔하려면 디렉토리의 root에 두어야한다.
//excludeFilters 의 Filter classes 설정은 해당 class를 제외하고 스캔함을 의미한다.
@Configuration
public class AutoAppConfig {

}
```

<br/>

추가적인 내용. Spring 에선 수동(Xml, Configuration)으로 등록된 Bean이 자동(Scan)으로    등록된 Bean보다 우선권을 가지며, 재 정의를 해버립니다.

하지만 스프링 부트에선 수동 빈 등록과 자동 등록 빈이 충돌이 나면 에러를 발생시킵니다.

<br/>

해당 설정은 

```java
spring.main.allow-bean-definition-overriding=false
```

이다.