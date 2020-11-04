2020.10.23

<br/>

**Singleton**

- Bean의 Default Scope로 Application 구동 시 컨테이너에 로드되며, 실행이 종료될 때 까지 **하나의 객체로써 존재**하게 된다.
- **컨테이너 생성 시점에 Bean 이 초기화 된다.**

<br/>

**Request**

- Client 의 요청이 왔을 때 사용하는 Bean이 해당 Scope라면, 그때 생성하여 의존관계를 주입하여 반환하고 **해당 요청이 Response 하게 되면 Bean 의 Life Cycle가 끝난다.**
- **하나의 요청 안에서 사용할 객체를 선언할 때 사용한다.**
- 컨테이너가 해당 Bean의 Life Cycle 을 계속 관리해준다.

<br/>

**Prototype**

- Client 의 요청이 왔을 때 사용하는 Bean이 해당 Scope라면, 해당 Bean을 생성하여    의존관계 주입 후 반환하고 **이후 Life Cycle의 관리를 개발자에게 넘긴다.**
- Bean 생성 → 의존관계 주입 → Bean 초기화 이후에는 개발자가 관리해야 한다
- **하나의 Bean Definition에 대해서 여러 객체를 생성하여 사용할 수 있다.**

<br/>

**Session** 

- 하나의 HTTP Session과 동일한 생명주기를 가지는 Scope (Session Time Out)

**Global-Session**

- 하나의 PortletContext 와 동일한 생명주기를 가지는 Scope

**application**

- 하나의 ServletContext 와 동일한 생명주기를 가지는 Scope

**websocket**

- 하나의 Web Socket과 동일한 생명주기를 가지는 Scope

<br/>

**Bean 참조**

<br/>

**Prototype Bean → Singleton  Bean** 

- 문제 없이 동작한다.

**Singleton Bean → Prototype Bean** 

**Prototype Bean이 Singleton Bean 처럼 사용된다.**

- Singleton 은 컨테이너 생성 시점에 등록되기 때문에 의존관계를 가지는 **Prototype Bean도 그 시점에서 요청하여 생성한다.**
- Singleton Bean은 내부적으로 생성된 **Prototype의 Bean의 참조값을 가지고 있다.**
- Singleton Bean으로 참조하는 Prototype Bean이 변경되지 않는다.
- **즉 새로운 빈이 생성되지 않고 참조된 것을 사용하고 있다.**

사용할 때마다 새로 생성해서 사용하고 싶다면?

<br/>

**해결 방법?**

- **ApplicationContext.getBean()**

    T ac = new **ApplicationContext;  → T ProtoBean = ac.getBean(ProtoBean.class)**

    **필요할 때마다 Spring Container 에게 요청하여 객체를 반환받는 방식이다.**

    해당 방법을 **DI**가 아니라 **DL** ( **Dependency Lookup** )이라고 한다. 
    
    <br/>

- **Scoped-proxy (@Scope(... proxyMode = "ScopedProxyMode.TARGET.CLASS"))**

    **ProxyMode** = default **→** TARGET.CLASS 로 설정 ( **CGI를 통한 Dynamic Proxy** )

    **Class 기반의 Proxy를 생성**하여 해당 Proxy를 통해 사용하게 해라/ **직접 참조 방지**

    **직접 참조**해버리면 **해당 객체를 변경할 여지가 존재하지 않기 때문**이다. 

    **자바의 JDK API 로의 Dynamic Proxy 생성은 인터페이스만 가능하다.**

<br/>

- **Obejct Provider**

    ObjectFactory 를 상속받아 편의 기능을 추가한 녀석이다. 스프링에 의존적이다.

    지정한 빈을 컨테이너에서 대신 찾아주는 DL 서비스를 제공한다.

    ```java
    @Scope("prototype")
        static class PrototypeBean {

            private int count = 0;

            public void addCount() {
                count++;
            }

            public int getCount() {
                return count;
            }

            @PostConstruct
            public void init() {
                System.out.println("PrototypeBean.init " + this);
            }

            @PreDestroy
            public void destroy() {
                System.out.println("PrototypeBean.destroy");
            }
        }

    ----------------------------------------------------

    @Autowired
    private ObjectProvider<PrototypeBean> prototypeBeanProvider;

    PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
    prototypeBean.addCount();
    return prototypeBean.getCount();
    ```

<br/>

- **Provider - 표준**

    JSR-330 표준의 기능이다.

    - javax.inject:javax.inject:1 라이브러리를 추가해야 사용이 가능하다.
    - get() 이라는 **하나의 기능을 가짐**으로 단위테스트를 만들거나 mock 코드를        만들기 매우 편하다.
    - lazy retrieval or Optional retrieval and 순환 참조가 발생할 때 사용하면 된다.

    ```java
    위와 동일
    ----------------------------------------------------

    @Autowired
    private Provider<PrototypeBean> prototypeBeanProvider;

    PrototypeBean prototypeBean = prototypeBeanProvider.get();
    prototypeBean.addCount();
    return prototypeBean.getCount();
    ```

<br/>

- **@Lockup**