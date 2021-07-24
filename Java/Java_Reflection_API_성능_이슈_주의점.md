2021.02.02

<br/>

### Reflection이란?

---

Compile Time에 Class나 Method 명을 알지 못하더라도 Runtime에 Type, Classpath를 이용하여 인스턴스화, 객체의 상태, 메서드 정보 등을 가져올 수 있도록 지원하는 API이다.

**사용하는 Library, Framework, API, Feature**

- Jackson, GSON 등의 **JSON Serialization Library**
- Log4 j2, Logback 등의 **Logging Framework**
- Apache Commons BeanUtils 등의 **Class Verification API**
- Spring의 @Autorwired와 같은 **DL, DI** 기능 (: processInject(), inject() Method )

    내부적으로 Spring의 ReflectionUtils라는 **Abstraction Library**를 사용한다.

- Eclipse, Intellij 등의 **IDE,** Junit, Hamcrest와 같은 **Test Framework**

***등이 있다.***

<br/>

### Refliection Flow?

---

1. JVM의 ClassLoader는. class를 Perm Gen (8+ : Metaspace)에 Load 한다.
2. Class 형식의 Instance가 생성된다. Type.class 형식으로 Heap에 저장된다.
3. Animal.class, animal.getClass(), = Class.forName(Classpath) 등으로 접근 가능하다.

    ```java
     // 1
     Class<Animal> clazz = Animal.class;

     // 2
     Animal animal = new Animal();
     Class<? extends Animal> aClass = animal.getClass();

     // 3try {
     Class<?> aClass1 = Class.forName("...Animal");
     } catch (ClassNotFoundException e) {
             e.printStackTrace();
     }
    ```

<br/>

### Reflection API 사용 시 장단점

---

<br/>

**장점**

- Runtime 시점에서 사용할 Instance를 선택하고 동작시킬 수 있는 유연성을 제공한다.
- 특정 객체를 감싸 추가적인 기능을 제공할 수 있다.

    RTW : JDK Dynamic Proxy

<br/>

**단점**

- Compile time에 Type, Exception 등의 검증을 진행할 수 없다.

    Runtime에서 가져오기때문

- Runtime에서 Instance가 선택되기 때문에 해당 로직의 구체적인 동작 흐름을 파악하는 것에 대해 어려움을 가지게 된다.
- Private 접근 제어자로 캡슐화된 필드, 메서드에 대해 접근 가능하기 때문에 기존 동작을 무시하고 깨트리는 행위가 가능해진다.

    Singleton 객체, Internal API 사용 등

- Java 보안 관리자에게 Runtime 때 특정 권한을 지정받게 되는데, 이는 Linux의 Root 계정처럼 보안 취약점을 만들고, 제약 사항을 위반할 수 있다.

<br/>

### Reflection 성능 이슈?

---

"Java Reflection API가 느리고 높은 비용을 사용한다"라는 이야기는 흔히 듣게 되는 이야기이며, 이는 Reflection API의 Method Invoke() 실행 시간을 측정하는 ( 정적 메서드 디스패치와 비교하는 ) 많은 테스트들에서 나타나는 결과이다.

하지만 이는 Reflection만을 테스트하는 것이 아니라, **동적으로 Class를 Load 하고, Heap에 객체를 띄우는 선행 절차가 존재하기에 나타나는 결과이다.**

그렇다고 Reflection API가 느리지 않고, 동일한 비용을 사용한다는 것은 아니다. 그러한 이유 중 하나로는 Reflection을 통한 초기 호출 시 JVM이 해당 정보를 미리 최적화할 수 없기 때문이다.

JIT Compiler의 Bytecode Caching, Opcode Optimization.. 등

<br/>

즉 초기 호출 이후로는 캐싱을 통해서 Reflection API를 통한 메서드 호출도 최적화된다는 것을 의미한다.

초기 호출에서는 5배 이상의 차이를 보이더라도 이후 호출부터는 그러한 간격이 줄어들게 된다. setAccessible과 같은 설정 기능들을 사용하는 경우에는 그렇지 않을 수 있다.

<br/>

**참고 자료**

- [https://www.doc.ic.ac.uk/~livshits/papers/pdf/aplas05r.pdf](https://www.doc.ic.ac.uk/~livshits/papers/pdf/aplas05r.pdf)
- [https://docs.oracle.com/cd/E13150_01/jrockit_jvm/jrockit/geninfo/diagnos/underst_jit.html](https://docs.oracle.com/cd/E13150_01/jrockit_jvm/jrockit/geninfo/diagnos/underst_jit.html)
- [https://hub.jmonkeyengine.org/t/java-reflection-isnt-slow/40196/16](https://hub.jmonkeyengine.org/t/java-reflection-isnt-slow/40196/16)