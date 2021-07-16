2020.12.26

## **Proxy Pattern?**

실제 비즈니스 로직과 그외의 코드를 분리시키는 역할을 수행하는 객체를 만드는 패턴이다.

서비스 객체를 호출하는 지점에 대리자 객체를 호출하여 그외의 코드를 수행시키고 프록시 객체에서 실제 서비스 객체를 호출한다.

<br/>

클라이언트는 프록시를 통해 서비스 객체 기능을 사용하기에 

- 접근 권한
- 부가기능
- 리턴 값 변경
- 로깅
- 트랜잭션 등

이러한 기능을 추가 할 수 있다.

**즉 서비스 객체 기능의 SRP를 해치지 않고 프록시를 통해 책임을 추가할 수 있다.**

**해당 Pattern의 특징**

1. 프록시 객체는 실제 서비스 객체와 같은 이름의 메서드를 구현한다.
2. 프록시 객체는 합성을 통해 서비스 객체에 대한 참조 변수를 가진다.
3. 프록시 객체는 실제 서비스의 메서드를 호출하고 그 결과를 클라이언트에게 반환한다.
4. 프록시 객체는 실제 서비스 메서드의 호출 전, 후에 별도의 로직을 수행할 수 있다.

<br/>

### **Pure Java Proxy**

<br/>

**TargetObject Interface**

```java
public interface TargetObject {

    String someMethod(String name);

}
```

**TargetObjectImpl Class**

```java
public class TargetObjectImpl implements TargetObject {

    @Override
    public String someMethod(String name) {
        return "Real Subject method "+ name +"\n";
    }
}
```

**TargetObjectProxy Class**

```java
public class TargetObjectProxy implements TargetObject {

    TargetObjectImpl subject;

    {
        subject = new TargetObjectImpl();
    }

    @Override
    public String someMethod(String name) {
        System.out.println("Before proxy");

        return subject.someMethod(name) + ("After proxy\n");
    }
}
```

**PureProxyClient Class**

```java
public class PureProxyClient {

    public static void main(String[] args) {
        PureProxyClient client = new PureProxyClient();
        client.run("Lob");
    }

    public void run(String name){
        TargetObjectProxy proxy = new TargetObjectProxy();
        System.out.println(proxy.someMethod(name));
    }

}
```

```java
Before proxy
Real Subject method Lob
After proxy
```

<br/>

## **Dynamic Proxy?**

<br/>

**런타임에 특정 인터페이스들을 구현하는 클래스나 인스턴스를 만드는 기술을 말한다.**

- **Spring Data JPA**
- **Spring AOP (Method Invocation 을 사용하기에 메서드 래밸만 지원한다.)**
- **Mockito**
- **Hibernate - lazy initialzation**

<br/>

### **JDK Dynamic Proxy**

<br/>

Java 에서 지원하는 동적(런타임) Proxy 구현 방식이다.
특정 인터페이스들을 구현하는 클래스나 인스턴스를 만드는 기술이다.

Reflection API을 사용하여 Target Class의 method를 invoke() 를 통해 동작시킨다.

<br/>

**invoke?**  

클래스의 이름과 인자 값을 넘겨서 객체의 메서드를 실행시키는 메서드이다.

인자 값을 이용하여 메서드를 실행시키게 된다.

<br/>

**제약**

Interface 기반의 Proxy → 모든 Target Class 는 Interface 를 implement 하고 있어야 한다.

<br/>

**JDK Dynamic Proxy 를 사용하는 라이브러리**

- Spring AOP **ProxyFactory** ( **JDK Dynamic Proxy 를 추상화하고 재정의한 Class**)
    - Spring Boot 에서는 CGLIB 를 기반으로 Spring AOP를 지원한다고 한다.
- Spring Data JPA **RepositoryFactorySupport** (BeanClassLoaderAware, BeanFactoryAware)

<br/>

**TargetObject Class**

```java
public interface TargetObject {

    void someMethod(String name);

}
```

**TargetObjectImpl Class**

```java
public class TargetObjectImpl implements TargetObject {

    @Override
    public void someMethod(String name) {
        System.out.println("Real Subject Do something " + name);
    }
}
```

**DynamicProxyClient Class**

```java
public class DynamicProxyClient {

    public static void main(String[] args) {
        DynamicProxyClient dynamicProxyClient = new DynamicProxyClient();

        dynamicProxyClient.run("Lob");
    }

    public void run(String name) {
        realObject.someMethod(name);
    }

    TargetObject realObject = (TargetObject) Proxy.newProxyInstance(TargetObject.class.getClassLoader(), new Class[]{TargetObject.class},
            (InvocationHandler) (proxy, method, args) -> {
                TargetObject targetObject = new TargetObjectImpl();

                System.out.println("Before Proxy");
                Object invoke = method.invoke(targetObject, args);
                System.out.println("After Proxy");

                return invoke;
            });
}

```

```java
Before Proxy
Real Subject Do something Lob
After Proxy
```

<br/>

### **CGLIB Dynamic Proxy (Spring, Hibernate - embed Library)?**

<br/>

코드 생성 라이브러리(Code Generator Library), 런타임에 동적으로 자바 클래스의 프록시를 생성해주는 기능을 제공한다.

<br/>

**장점**

- 실제 바이트 코드를 조작하여 JDK Dynamic Proxy 보다 상대적으로 빠르다.
- CGLIB 프록시는 Interface 기반의 Proxy 가 아니라 Target Class 를 상속받아 생성한다.
- final 이나 private 같이 상속된 객체에 Overriding 을 제공하지 않는다면 해당 행위에 대해서 Aspect 를 적용할 수 없다.

**제약**

- 버전별 호환성이 좋지 않다. (사용 라이브러리 내부에 내장시켜 제공한다.)

<br/>

**CGLIB Dynamic Proxy 를 사용하는 라이브러리**

- Spring Boot AOP (Proxy)
- Hibernate
- ...

<br/>

**TargetObject Class**

```java
public class TargetObject {

    void someMethod(String name) {
        System.out.println("Real Subject Do something " + name);
    };

}
```

**CglibDynamicProxyClient Class**

```java
public class CglibDynamicProxyClient {

    public static void main(String[] args) {
        CglibDynamicProxyClient client = new CglibDynamicProxyClient();
        client.run("Lob");
    }

    public void run(String name) {

        MethodInterceptor methodInterceptor = new MethodInterceptor() {
            final TargetObject targetObject = new TargetObject();

            @Override
            public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
                System.out.println("Before Proxy");
                Object invoke = method.invoke(targetObject, args);
                System.out.println("After Proxy");
                return invoke;
            }
        };

        TargetObject targetObject = (TargetObject) Enhancer.create(TargetObject.class, methodInterceptor);

        targetObject.someMethod(name);
    }
}
```

```java
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by net.sf.cglib.core.ReflectUtils$1 (file:/C:/Users/serrl/.gradle/caches/modules-2/files-2.1/cglib/cglib/3.3.0/c956b9f9708af5901e9cf05701e9b2b1c25027cc/cglib-3.3.0.jar) to method java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain)
WARNING: Please consider reporting this to the maintainers of net.sf.cglib.core.ReflectUtils$1
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
Before Proxy
Real Subject Do something Lob
After Proxy
```

<br/>

**예제 Repo**

[Lob-dev/JavaEE-DesignPattern](https://github.com/Lob-dev/JavaEE-DesignPattern/tree/master/src/main/java/lob/code/proxy)

<br/>

### 참고 자료

- [https://www.inflearn.com/course/the-java-code-manipulation/dashboard](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)
- [https://www.baeldung.com/cglib](https://www.baeldung.com/cglib)
- [https://www.baeldung.com/java-dynamic-proxies](https://www.baeldung.com/java-dynamic-proxies)