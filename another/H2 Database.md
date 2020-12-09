2020.12.10

### **H2 Embedded (In-Memory) 모드와 Server (TCP) 모드?**

<br/>

**Embedded 모드**

시스템의 메인 메모리에서 구동시키는 방식으로 application이 종료된다면 저장, 수정된 Data가 손실(휘발) 된다. 즉 영구적이지 않은 방식이다.

데이터에 대한 영속성을 제공하는 방법이 있지만, 그럴 경우에는 그냥 Server 모드를 사용하는 것이 좋을 것 같다. 주로 개발 단계에서 테스트에 사용된다.

<br/>

**YAML 설정**

```yaml
spring:
  datasource:
    url: jdbc:h2:mem:testdb # in-memory 주소 설정
    username: sa # 기본 계정명 password 를 추가하지 않는다면 기본적으로 없음으로 구동한다.
    driver-class-name: org.h2.Driver 
  h2:
    console:
      enabled: true # in-memory 방식을 사용하는 경우 H2 콘솔을 사용할 것인지의 유무이다.
```

처음에 H2 데이터베이스를 사용하기 위하여선

```yaml
jdbc:h2/~/testdb
```

해당 주소를 입력하고 한번 구동시켜야 한다. 이는 해당 DB의 파일을 생성하는 방법이다. 

**→ 사용자 디렉터리에 data base 파일이 생성된다.** 

해당 모드는 기본적으로 데이터가 저장되어 있지 않기 때문에 SQL파일을 통한 초기화가 선행되어야 한다. 스프링 부트에서는 com.domain.main.resource 밑에 schema.sql 과 data.sql 파일이 있다면 자동으로 해당 파일들을 이용하여 DB를 구성하게 된다.

<br/>

**Server 모드**

**별도의 프로세스를 통해 동작시키며 H2 데이터베이스를 영구적으로 사용하는 방법이다.**

localhost의 8082포트를 통해 DB 콘솔에 접근할 수 있으며, 데이터를 계속해서 보관하게 된다.

```yaml
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/test # Tcp, Server 모드 주소 설정
    username: sa
    driver-class-name: org.h2.Driver 
```