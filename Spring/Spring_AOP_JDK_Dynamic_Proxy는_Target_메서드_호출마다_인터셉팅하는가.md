2021.02.02

<br/>

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7cf869d0-be36-4710-9dda-ae3091fb1d7a%2FUntitled.png?table=block&id=8e89d102-2e42-4855-a5b1-f06f165ad4b2&spaceId=3f0ac77b-a821-45cf-b1be-d98cfb99a8c7&width=3080&userId=6c65ec07-595a-45d5-a0cf-68530a8bac2d&cache=v2" width="50%">


PostProcessor를 통해 Process가 진행되고, Reader를 통해 읽어 들여진 Bean Definition에 대해서 선언적인 AOP, Transaction 등이 적용되었다면, Proxy 객체를 생성하고, 해당 객체를 ApplicationContext에 반환하게 된다. 그리고 business Logic에서 DI가 있어야 하였을 때, 해당 Proxy 객체를 반환하여 그를 통해 Logic을 실행하게 된다.

결국 Business Logic에서 발생하는 모든 객체 호출은 Proxy를 통해 요청되는 흐름을 가지게 된다.

```java
@Autowired
XxxService xxxService;

// 는 이것과 같다.
// 사실 이부분은 PostProcessor를 통한 Bean 등록시 반환되어 Context에 관리되는 Proxy 
// 객체를 DI 해주는 것이다. 
XxxService xxxService = (XxxService) Proxy.newProxyInstance(
		XxxService.class.getClassLoader(), new Class[]{XxxService.class},
            (InvocationHandler) (proxy, method, args) -> {
                XxxService xxxService = new DefaultXxxService();
								Method targetMethod = null;

								// Verification?
								if (proxyMethodMap.containsKey(method)) {
										targetMethod = cacheMethodMap.get(method);
								} else {
										Object invoke = method.invoke(xxxService, args);
										return invoke;
							  }

                // Before Proxy....

								// Invoke
                Object invoke = targetMethod.invoke(xxxService, args);
                
								// After Proxy....

                return invoke;
            });

```

**Java Code로 확인해보기**

```java
public interface TargetObject {

    void someMethod(String name);

    default void someMethodA() {
        System.out.println("Interface.someMethodA");
    }

    default void someMethodB() {
        System.out.println("Interface.someMethodB");
    }

}

public class TargetObjectImpl implements TargetObject {

    @Override
    public void someMethod(String name) {
        System.out.println("Real Subject Do something " + name);
    }
}

public class DynamicProxyClient {

    public static void main(String[] args) {
        DynamicProxyClient dynamicProxyClient = new DynamicProxyClient();

        dynamicProxyClient.run("Lob");
    }

    public void run(String name) {
        realObject.someMethod(name);
        realObject.someMethodA();
        realObject.someMethodB();
    }

    TargetObject realObject = (TargetObject) Proxy.newProxyInstance(TargetObject.class.getClassLoader(), new Class[]{TargetObject.class},
            (proxy, method, args) -> {
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
Before Proxy
Interface.someMethodA
After Proxy
Before Proxy
Interface.someMethodA
After Proxy
```