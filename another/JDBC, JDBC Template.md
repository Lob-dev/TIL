2020.12.15

### **Java Database Connectivity?**

- Java와 여러가지 데이터베이스들 간의 연결을 위하여 제공되는 표준 인터페이스이다.
    - DB 벤더나, 써드파티 관련 라이브러리에서 JDBC를 구현한 드라이버를 제공한다.
- DB 데이터 접근를 위해 계층 처리 모델을 제공한다. (기본 2계층 사용, 3계층 지원)

**데이터베이스 벤더마다 각각의 SQL 문, 작성 방식을 사용함으로써 발생했던 문제점을 해결.**

- **달랐던 메서드, 구조, 전역 변수 등의 모든 문법을 통일시켜 API로 명세한 것이다.**

<br/>

### **JDBC 구성 요소**

<img src="./../image/JDBC%20Layer.png" width="90%"></img>


**DriverManager**

- DB의 드라이버 목록을 관리하는 클래스
- Java Application의 연결 요청을 적절한 DB Driver와 매핑시킨다.

**DatabaseDriver**

- DB 서버의 통신을 처리한다.

**Connection**

- DB와 연결하기 위한 모든 메세지가 포함된 인터페이스
- DB 와의 통신은 연결 개체를 통해서만 이루어진다.

**Statement**

- 해당 인터페이스의 구현체가 SQL 문을 DB에 전달한다.
- 일부 파생 인터페이스는 저장 프로시저를 실행하는 것 외에도 매개 변수를 허용한다.

**PreparedStatement** 

- Statement의 하위 인터페이스.

**ResultSet**

- Statement 객체를 사용하여 SQL 쿼리를 실행한 뒤 데이터 베이스에서 검색, 반환된 데이터를 저장한다. (쿼리에 대한 결과를 나타낸다.)
- 데이터 이동시키는 일종의 이터레이터의 역할을 한다.

**SQLException**

- 해당 클래스는 DB 에서 발생하는 모든 오류를 처리한다.

<br/>

### **JDBC의 처리 흐름**

- JDBC 클래스가 포함된 패키지를 로드하고 JDBC 드라이버를 등록하여야 한다.
- DriverManager.getConnection() 혹은 datasource.getConnection() 를 이용하여 Connection 객체를 얻는다.
- Statement 타입의 객체를 사용하여 쿼리를 실행한다.
- 결과가 있는 경우 반환된 ResultSet.getXXX() 메서드를 통하여 데이터를 추출한다.
- close 를 통하여 사용한 객체들의 대한 리소스를 풀에 반환한다.

<img src="./../image/JDBC%20Flow.png" width="90%"></img>

Connection과 PreparedStatement 객체는 보통 Pool 방식으로 운영되며, 미리 정의된 제한된 수의 리소스를 만들어두고 필요할 때 할당하고 반환하면 다시 풀에 넣는 방식으로 동작한다.

close()가 동작되지 않으면 풀에 저장된 리소스는 계속해서 줄어들게 되고, 고갈됨으로써 문제를 일으키게 된다. 

JDBC 코드를 작성할 때에는 예외처리를 하여야하며, 어떤 상황이 발생되더라도 리소스가 반환되게 코드를 작성하여야 한다. 

<br/>

**모든 상황에서 리소스를 반환하기 위한 예외처리 방법**

- **try-catch-finally 방식**

    ```java
    try {
    		// DriverManager.getConnection(sqlPath, user, pw);
    		con = dataSource.getConnection(); 
    		ps = c.prepareStatement(SQL);
    		ps.excuteUpdate();
    } catch (SQLException e){
    		e.stackTrace();
    } finally {
    		if (ps != null) { try { ps.close(); } catch (SQLException e) {}}
    		if (con != null) { try { con.close(); } catch (SQLException e) {}}
    }
    ```

    복잡한 흐름을 가짐으로써 가독성을 떨어트리게 된다. (이 예시는 매우 하는 것이 없는편)

- **try-with-resources 방식**

    ```java
    try (con = dataSource.getConnection(); ps = c.prepareStatement(SQL);) {
    		ps.excuteUpdate();
    } catch (SQLException e) {
    		e.stackTrace()
    }
    ```

    해당 방식은 내부적으로 close를 호출함으로써 리소스를 반환한다.

<br/>

### **JDBC Template**

Spring은 JDBC의 반복되는 보일러 플레이트 코드를 템플릿, 콜백 패턴을 적용한 라이브러리인 Spring JDBC Template 을 제공한다.

- **DB 연결, SQL 질의에 대한 사용한 리소스를 내부적으로 정리하고 풀에 반환한다.**
- **JDBC SQLException (checked)을 RuntimeException으로 변환하여 제공함으로써, 유연한 대응을 할 수 있게끔 지원한다. (예외 처리 지원)**
- **다양한 SQL 질의 방식과 결과 추출을 제공한다.**
    - ResultSetExtractor : 단일 객체에 대한 데이터 바인딩 제공 (Post, Member ....)
    - RowMapper : 객체 목록에 대한 데이터 바인딩 제공 (List, Map ....)
- **멀티 스레드 환경에서 안전한 객체들을 제공한다.**

**JDBC의 복잡했던 사용 방식을 저수준의 모듈, 메서드에 모아놓고 호출, 사용한다.**

<br/>

### **JdbcTemplate의 구성 객체**

<br/>

**JdbcTemplate**

- JDBC Template의 Core Class
- JDBC 사용을 단순화하고 일반적인 예외를 방지하는 데 기능을 제공한다.
- 설정된 Datasource 를 사용하여 JDBC Template 객체를 생성하게 된다.

**PreparedStatementSetter**

- JDBC Template Class가 사용하는 Callback Interface
- JDBC Template가 생성한 PreparedStatement의 매개 변수 값을 설정한다.

**ResultSetExtractor**

- JDBC Template 질의 결과를 추출하는데 사용되는 Interface
- ResultSet에서 결과를 추출하는 실제 작업을 수행한다.
- 내부적으로 하나의 객체에 대해서 데이터를 Binding할 때 사용되는 Interface이다.

**RowMapper**

- JDBC Template 질의 결과를 추출하는데 사용되는 Interface
- 쿼리 질의 결과에 각각의 행들을 객체에 Mapping하는데 사용된다.

**그외에도** 

- **NamedParameterJdbcTemplate  :**  ? 를 통한 값 대입 대신 :param 형식을 사용할 때 이용
- **SimpleJdbcTemplate**
- **SqlQuery**
- **SqlUpdate**
- **StoredProcedure**

등이 존재한다.

<br/>

### **Statement vs PreparedStatement**

<br/>

**Statement의 Flow?**

1. 쿼리 문장의 분석
2. 쿼리 컴파일 
3. 쿼리 실행

Statement는 매 작업시 위의 작성된 flow를 따르게 된다. 

PreparedStatement는 이미 반환한 트랜잭션에 대해서는 (주로 조회성) 캐싱을 함으로써 이미 실행한 쿼리에 대해서는 불 필요한 Flow를 진행하지 않고 바로 값을 반환하게 된다.

<br/>

**Statement의 String SQL?**

- Statement 객체는 SQL 쿼리를 문자열 변수를 통하여 받게된다. Param들에 대해서도 문자열 연산을 통하여 제공받아야 되는데 이는
    - 코드의 가독성을 떨어트리고
    - SQL 인젝션에 취약한 환경을 만들며
    - 쿼리의 최적화가 되지 않는다.

    ```java
    String final query = "INSERT INTO PERSON(ID, NAME) VAULES("+postPerson.getId()+"
    				","+postPerson.getName()+")";

    ```

**PreparedStatement의 String SQL?**

- PreparedStatement는 인자로 넘기는 문자열에 대하여서 parameters 기능을 제공한다.

    ```java
    String final query = "INSERT INTO PERSON(ID, NAME) VALUES(?, ?)";
    PreparedStatement pr = con.preparedStatement(query);

    for (PostPerson person : PostPersons){
    		pr.setInt(1, person.getId());
    		pr.setString(2, person.getName());
    		pr.addBatch();
    }
    preparedStatement.executeBatch();
    ```

    코드가 더 길어보이지만, 가독성을 생각하면 좀 더 나은 모습을 보인다.

    PreparedStatement는 인자로 넘기는 문자열에 대하여서 parameters 기능을 제공한다.

    ```java
    String final query = "INSERT INTO PERSON(ID, NAME) VALUES(?, ?)";
    PreparedStatement pr = con.preparedStatement(query);

    for (PostPerson person : PostPersons){
    		pr.setInt(1, person.getId());
    		pr.setString(2, person.getName());
    		pr.addBatch();
    }
    preparedStatement.executeBatch();
    ```

    코드가 더 길어보이지만, 가독성을 생각하면 좀 더 나은 모습을 보인다.

<br/>

### **PreparedStatement의 사전 컴파일, 일괄 실행?**

<br/>

**사전 컴파일?**

- DB가 SQL을 받았을 경우 우선적으로 캐시를 확인하게끔 동작한다.
- 캐시가 존재한다면 해당 정보를 제공하고, 캐시되지 않은 경우 쿼리를 실행하고 데이터를 캐싱한다.
- 해당 기능은 non-SQL binary protocol 를 내부적으로 사용함으로써 DB와 JVM간의 통신 속도를 높인다. (패킷에 데이터가 적기 때문에 서버 간의 통신이 빨라진다. )

**일괄 실행?**

- 대용량 쿼리를 한번에 전송 가능한 기능이다.
- addBatch를 통하여 쿼리를 메모리에 적재하고, executeBatch를 통하여 한번에 전송한다.
    - 매번 통신을 연결하고 데이터 전달을 진행하는 리소스들을 줄인다.
    - 한번에 여러 쿼리를 질의함으로써 불필요한 절차를 제거하고 빠르게 결과를 받는다.
    - Buffer 매커니즘을 활용한 방식이다.

<br/>

### **결론**

PreparedStatement는 Statement가 가지고 있던 불필요한 절차, 리소스 사용, Non-Caching 등의 요소들을 최적화한 하위 인터페이스 (구현체) 이다.

DB와의 질의 성능이 중요한 Server든 아니든간에 유의미한 성능차이를 보이므로, PreparedStatement를 사용하는 것이 좋다.