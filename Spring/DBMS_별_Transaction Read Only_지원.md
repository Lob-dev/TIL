2020.12.25

이번 프로젝트를 진행할 때 Post Service에 Transcation Read only 설정을 적용하게 되었다.

그 이유는 read only를 사용하면 read-write 보다 성능이 더 좋다고 들었기 때문이다.

하지만 해당 설정에 대하여서 DB마다 동작 방식이 다르다고 하여, 많이 사용되는 DB들을 기준으로 
조사해보았다. *근데 지금 보니 유사한 부분이 더 많더라..* 

<br/>

### **Oracle**

Read Only 트랜잭션을 이용할 경우 이 트랜잭션이 시작되기 이전에 커밋된 데이터만 접근할 수 있으며, 트랜잭션 실행되는 동안 커밋되는 데이터는 결과에 반영되지 않는다.

해당 트랜잭션 시에 지원하는 DML은 SELECT(조회) 구문 뿐이다. 

**→ 해당 트랜잭션 내에서 일관적인 데이터를 얻도록(데이터 일관성) 보장한다. 즉 성능 이점만을 위함이 아니다.** 

<br/>

### **PostgreSQL**

해당 트랜잭션을 이용할 경우 SELECT를 제외한 DDL, DML, DCL은 동작하지 않는다.

Postgresql 에서는 읽기 동작을 가정할 경우, Read/Write 속성과 성능 차이를 가지지 않으며 (해당 부분의 최적화 X) Deffered 속성(**행 단위가 아닌 트랜잭션 단위의 제약조건 검증 처리**)을 적용하였을 때, SERIALIZABLE 이거나 READ ONLY를 사용하게 되어 내부의 튜플이 수정되지 않는 안전한 동작을 하게끔 지원하는 용도이다. 

→ Postgre의 Read Only 설정도 성능 이점이 아닌 동시성 제어를 위함이다.

→ Postgre는 Read Only 설정시 트랜잭션 ID를 일반적인 ID가 아닌 가상 ID로 제공하기에     실제로 제공되는 트랜잭션 ID가 수가 줄어들어 **성능이 개선될 수도 있다.**

<br/>

**Postgresql의 튜플(Tuple)?**

MVCC를 지원하기 위해 사용되는 기능으로 **PostgreSQL은 트랜잭션 처리 시** 기존에 가지고 있는 **데이터 로우를 복제하여 기존 로우를 사용자들이 조회하는 용도로, 복제한 row를 트랜잭션 처리 용도 (데이터)로 사용**하게 되는데 이러한 원본, 복제본에 **버전을 명시할 때 사용되는 것이 튜플이다.**

삭제, 롤백, 업데이트 시에는 생성된 튜플들에 대하여서 Dead 라는 마킹이 적용되며 (실제 데이터가 삭제되지 않는다.) **VACUUM FULL** 를 통하여 해당 데이터들을 파기하게 된다.

<br/>

### **MySQL**

해당 트랜잭션을 이용할 경우 SELECT 문에 대해서만 기능을 지원하며, Transaction ID 설정에 대한 오버헤드를 해결할 수 있다. (Read Only 트랜잭션에 대해서는 ID가 부여되지 않는다. )

추가적으로 세션 별로 임시 테이블을 변경하거나 Lock 쿼리를 실행할 수 있는데, 이는 다른 트랜잭션(Read Write 혹은 다른 Read Only) 들이 가시할 수 없는 범위이기 때문이다.

Oracle과 마찬가지로 별도의 스냅샷을 통해 데이터를 조회하기 때문에, 데이터 일관성을 보장할 수 있다.

**→ 트랜잭션 ID 설정에 대한 오버헤드를 해결하고, 스냅샷을 통해 데이터 일관성을 보장한다.**

<br/>

### **Read Only 설정 방식?**

Oracle은  9i, 10g 버전에서 Connection.setReadOnly 를 통해 해당 트랜잭션을 지원하였다.

11, 12c 드라이버를 사용하는 경우에 Connection.setReadOnly(true)를 설정하더라도 Read only 트랜잭션이 설정, 동작하지 않는다.

- 이것을 해결하기 위해 DataSourceTransactionManager에 repareTransactionConnection Template method가 추가되었으며, setEnforceReadOnly 설정을 true로 설정하면 prepareTransactionalConnection를 통한 DB와 커넥션 연결시에 SET TRANSACTION READ ONLY 구문을 보냄으로써 Read Only Transaction을 사용할 수 있게 되었다.

    **해당 설정은 스프링 4.3.7 이후에 추가되었다. (Oracle, MySQL, Postgre에 적용 가능)**

    ```java

    // DataSourceTransactionManager.java

    private boolean enforceReadOnly = false;

    /**
    	 * Specify whether to enforce the read-only nature of a transaction
    	 * (as indicated by {@link TransactionDefinition#isReadOnly()}
    	 * through an explicit statement on the transactional connection:
    	 * "SET TRANSACTION READ ONLY" as understood by Oracle, MySQL and Postgres.
    	 * <p>The exact treatment, including any SQL statement executed on the connection,
    	 * can be customized through {@link #prepareTransactionalConnection}.
    	 * <p>This mode of read-only handling goes beyond the {@link Connection#setReadOnly}
    	 * hint that Spring applies by default. In contrast to that standard JDBC hint,
    	 * "SET TRANSACTION READ ONLY" enforces an isolation-level-like connection mode
    	 * where data manipulation statements are strictly disallowed. Also, on Oracle,
    	 * this read-only mode provides read consistency for the entire transaction.
    	 * <p>Note that older Oracle JDBC drivers (9i, 10g) used to enforce this read-only
    	 * mode even for {@code Connection.setReadOnly(true}. However, with recent drivers,
    	 * this strong enforcement needs to be applied explicitly, e.g. through this flag.
    	 * @since 4.3.7
    	 * @see #prepareTransactionalConnection
    	 */
    public void setEnforceReadOnly(boolean enforceReadOnly) {
    		this.enforceReadOnly = enforceReadOnly;
    	}

    /**
    	 * Return whether to enforce the read-only nature of a transaction
    	 * through an explicit statement on the transactional connection.
    	 * @since 4.3.7
    	 * @see #setEnforceReadOnly
    	 */
    public boolean isEnforceReadOnly() {
    		return this.enforceReadOnly;
    	}

    /**
    	 * Prepare the transactional {@code Connection} right after transaction begin.
    	 * <p>The default implementation executes a "SET TRANSACTION READ ONLY" statement
    	 * if the {@link #setEnforceReadOnly "enforceReadOnly"} flag is set to {@code true}
    	 * and the transaction definition indicates a read-only transaction.
    	 * <p>The "SET TRANSACTION READ ONLY" is understood by Oracle, MySQL and Postgres
    	 * and may work with other databases as well. If you'd like to adapt this treatment,
    	 * override this method accordingly.
    	 * @param con the transactional JDBC Connection
    	 * @param definition the current transaction definition
    	 * @throws SQLException if thrown by JDBC API
    	 * @since 4.3.7
    	 * @see #setEnforceReadOnly
    	 */
    protected void prepareTransactionalConnection(Connection con, TransactionDefinition definition)
    			throws SQLException {

    		if (isEnforceReadOnly() && definition.isReadOnly()) {
    			try (Statement stmt = con.createStatement()) {
    				stmt.executeUpdate("SET TRANSACTION READ ONLY");
    			}
    		}
    	}
    ```

<br/>

### **Read only Transaction을 사용하는 일반적인 이유**

- 해당 설정을 사용한 트랜잭션은 데이터(테이블, 컬럼) 에 대하여 Lock을 적용할 필요가 없고 접근할 수 있는 데이터가 (스냅샷, 튜플 등) 변경되지 않기 때문에, 일관적인 데이터를 읽어오고 제공할 수 있다.
- 해당 속성의 경우 일반적으로 트랜잭션 ID 를 부여하지 않아도 되기에 불필요한 ID 설정에 대한 오버헤드가 발생하지 않기 때문에 성능의 이점을 볼 수 있다.

    → 추가적으로 읽기-쓰기 트랜잭션을 위해 구성되는 별도 스냅샷의 총 수가 줄어든다.

<br/>

**참고 자료**

- [https://stackoverflow.com/questions/33332706/does-postgresql-run-some-performance-optimizations-for-read-only-transactions](https://stackoverflow.com/questions/33332706/does-postgresql-run-some-performance-optimizations-for-read-only-transactions)
- [https://dev.mysql.com/doc/refman/8.0/en/innodb-performance-ro-txn.html](https://dev.mysql.com/doc/refman/8.0/en/innodb-performance-ro-txn.html)
- [https://neo-orcl.tistory.com/90](https://neo-orcl.tistory.com/90)
- [https://github.com/spring-projects/spring-framework/blob/885f6dbab94712fa76545276058a62216e17881e/spring-jdbc/src/main/java/org/springframework/jdbc/datasource/DataSourceTransactionManager.java#L214](https://github.com/spring-projects/spring-framework/blob/885f6dbab94712fa76545276058a62216e17881e/spring-jdbc/src/main/java/org/springframework/jdbc/datasource/DataSourceTransactionManager.java#L214)
- [https://linux.systemv.pe.kr/튜플tuple-과-백쿰vacuum/](https://linux.systemv.pe.kr/%ED%8A%9C%ED%94%8Ctuple-%EA%B3%BC-%EB%B0%B1%EC%BF%B0vacuum/)