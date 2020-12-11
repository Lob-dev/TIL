2020.12.10

## H2의 Local, Server 개념

<br/>

### **Embedded 모드**

<img src="./../image/memory%20mode.png" width="90%"></img>

H2 DB를 시스템의 메인 메모리에서 (**JVM 위에서**) 구동시키는 방식으로 application이 종료된다면 저장, 수정된 Data가 손실(휘발) 된다. 즉 기본적으로는 영속적이지 않은 방식이다.

→ 데이터에 대한 영속성을 제공하는 방법은 존재한다.

<br/>

메인 메모리에 DB를 띄워놓고 해당 DB를 사용하는 Application의 쓰레드로 데이터에 바로 접근함으로써 데이터 읽기, 쓰기에 대한 성능을 향상시킬 수 있으므로 유용하게 사용할 수 있으며, 데이터 캐싱 DB에 대해서도 H2를 고려할 수 있다고 한다.

<br/>

하지만 JVM에서 데이터 연산에 사용되는 쓰레드를 인터럽트하지 않을 수 있기에, IO 수행 시에 I/O Handler가 닫힘으로써 데이터베이스의 손상을 일으킬 수 있다.

<br/>
<br/>

### **Server 모드**

<img src="./../image/server%20mode.png" width="90%"></img>

**해당 이미지는 하나의 시스템에서 서버 모드를 사용하는 경우이다.**

**별도의 프로세스(JVM)를 통해 DB를 동작시켜 데이터베이스를 영속적으로 사용하는 방법이다.**

local 환경에서는 localhost의 9092포트를 통해 DB 콘솔에 접근할 수 있으며, 별도의 서버 위에서 동작시킬 경우에 여러 Application을 해당 데이터베이스에 동시적으로 연결할 수 있다.

서버모드도 내부적으로는 Embedded 모드와 동일한 실행방식을 가지지만, 모든 데이터의 처리흐름이 TCP/IP 를 통하여 전송되기 때문에 Embedded 모드보다 상대적으로 느릴수 밖에 없다.

<br/>

## H2 사용 방식

**처음에 H2 데이터베이스를 사용하기 위하여선**

```yaml
jdbc:h2/~/testdb
```

해당 주소를 입력하고 한번 구동시켜야 한다. 이는 해당 DB의 파일을 생성하는 방법이다. 

**→ 기본적으로 사용자 디렉터리에 data base 파일이 생성되게 된다.**

<br/>

**Embedded - YAML 설정**

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

해당 모드는 기본적으로 데이터가 저장되어 있지 않기 때문에 SQL파일을 통한 DDL이 선행되어야  한다. 스프링 부트에서는 com.domain.main.resource 밑에 schema.sql 과 data.sql 파일이 있다면 자동으로 해당 파일들을 이용하여 DB를 구성하게 된다.

<br/>

**Server - YAML 설정**

```yaml
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/test # Tcp, Server 모드 주소 설정
    username: sa
    driver-class-name: org.h2.Driver 
```

<br/>

## Local Mode vs Server Mode

<br/>

### **어떤 것을 사용해야 할까?**

**Local Mode의 장점으로는**

- 메모리로 바로 접근함으로써 빠른 데이터(쿼리) 처리
- (스프링 부트가 지원하는) sql 파일을 통한 쉬운 DDL, DML 적용

정도가 있다고 생각한다.

<br/>

**단점으로는**

- JVM 시스템의 컨트롤로 인한 데이터베이스 손실 ( 쿼리, 연산 중 I/O Handler 종료)
- Application의 쓰레드와 자원을 사용함으로써 해당 Application의 상태에 영향을 받는다.

정도가 있는데, 해당 문제들은 테스트 DB로만 사용할 때에는 큰 영향이 없다고 생각된다.

<br/>
<br/>

**Server Mode의 장점으로는**

- 제한이 없는 Application과 DB의 커넥션 (TCP/IP 를 사용함으로써 흐름제어가 가능한 듯 하다.)
- PageStore 엔진의 동기화를 이용하여 멀리 스레딩을 지원함으로 데이터 정합성을 지킬 수 있다.

정도가 있는 것 같고,

<br/>

**단점으로는**

- TCP/IP 를 이용하여 데이터의 흐름을 가지기 때문에 상대적으로 커넥션을 맺고 데이터를 전송하는 등의 상대적인 성능 오버헤드가 존재한다.
- 외부 서버에서 제공하고 연동 설정을 지정하지 않는다면, 환경에 따라서 해당 모드를 사용하는 프로젝트에 대해 별도의 H2 설정과 데이터베이스에 대한 데이터 **(DDL, DML 등)** 가 요구된다.

정도가 있는 것 같다.

<br/>

### 결론

사실 프로젝트 진행 중에 빠르게 결과를 받고, 쉽게 관리하기 위하여서는 (DDL 을 변경하여 쉽게     구조를 적용한다던지 등 ) In-Memory 방식이 매우 간편하다. 

Server mode 는 콘솔을 이용해서 데이터에 대해 별도의 관리를 해야하기에 테스트 단계에서 DB 때문에 필요없는 복잡함을 가지게끔 하기도 하고, 프로젝트를 사용할 수 있는 여러 환경에서 다른 설정값이 존재할 수 있기에 신경을 써야한다.

그러니까..! In-Memory를 사용하자!

<br/>

### 참고 자료

- [http://h2database.com/html/features.html?highlight=server&search=server#firstFound](http://h2database.com/html/features.html?highlight=server&search=server#firstFound)