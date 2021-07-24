2021.02.06

<br/>

### **FactoryBean**

2021.02.06Dynamic Proxy는 일반적으로 Spring Bean으로 등록할 수 없다. 이는 Bean을 클래스 이름과 프로퍼티를 Reflection하여 Bean으로 만들어내기 때문이다. *즉 사전 정보를 가지고 등록한다.*

<br/>

Dynamic Proxy는 대상 클래스가 어떤 것인지를 사전에 알아낼 수 없다. 해당 클래스의 내부적으로 동적 재정의가 이루어지기 때문이다. *사전에 주어진 정보가 존재하지 않는다.*

이러한 Dynamic Proxy를 Bean으로 관리하기 위해서 사용하는 것이 FactoryBean이다. 

<br/>

FactoryBean은 스프링을 대신하여 객체의 생성로직을 담당하도록 만들어진 Bean이다. 이러한 Bean을 구현하는 제일 쉬운 방법은 FactoryBean 인터페이스를 구현하는 것이다.

```java
public interface FactoryBean<T> {
		// Bean을 생성하고 반환한다.
		T getObject() throws Exception;
		
		// 생성된 Bean의 Type을 반환한다. 
		Class<? extends T> getObjectType();

		// getObject()로 반환되는 Bean이 Singleton방식인지 확인한다.
		boolean isSingleton();
}
```

<br/>

이후 Dynamic Proxy 생성 로직을 getObject()에 작성하여 호출시 프록시 객체를 반환하게끔 구현한다.

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F57342b10-e80a-4dc1-91c5-c2c1fc6ae970%2FUntitled.png?table=block&id=3483ccbc-25a5-4400-bbeb-22442a0eaffe&spaceId=3f0ac77b-a821-45cf-b1be-d98cfb99a8c7&width=3080&userId=6c65ec07-595a-45d5-a0cf-68530a8bac2d&cache=v2" width="90%">

```java
// DI
Object target;
PlatformTransactionManager transactionManager;
Class<?> targetInterface;

public Object getObject() throws Exception {
		TransactionHandler handler = new TranscationHandler();
		handler.setTarget(target);
		handler.setTransactionManager(transactionManager);
		return Proxy.newProxyInstance(
				getClass().getClassLoader(), new Class[] { targetInterface }, handler);
}
```