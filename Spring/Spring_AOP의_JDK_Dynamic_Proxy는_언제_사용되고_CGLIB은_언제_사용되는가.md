2021.01.27

<br/>

**JDK Dynamic Proxy** 

Interface가 구현된 Spring Bean에 대해 Proxy를 적용할 때 사용한다.

<br/>

**CGLIB**

Interface가 구현되지 않은 Bean Class, @Configuration Class, Lite mode의 Bean 사용 시 해당 라이브러리를 통해 바이트코드를 만들게 된다.

<br/>

**CGLIB는 언제 Code를 Generate하는가?**

CGLIB는 Dynamic Loading 방식으로 Bytecode를 생성한다. 즉 Application이 실행된 이후 ApplicationContext에 Bean이 Loading 될 때 Proxy를 만들게 된다.

*CGLIB는 ASM을 통해 Bytecode를 계측하고 생성한다.*