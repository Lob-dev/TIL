2020.12.29

## TranscationDefinition interface와 Propagation, Isolation

Spring application 과 호환되는 트랜잭션 속성을 정의하는 인터페이스이다.


EJB CMT 속성과 유사한 전파 행위의 정의를 기반으로 한다.

Spring Framework의 트랜잭션은 새로운 설정이 적용되지 않는 한 기본적으로 PROPAGATION_REQUIRED, PROPAGATION_REQUIRES_NEW, PROPAGATION_NESTED 로 동작한다.

설정 변경시 모든 트랜잭션 관리자가 해당 설정을 지원하는 것은 아니기에 예외가 발생할 수 있다.

---
## 트랜잭션 **전파 속성**

트랜잭션의 경계를 설정할 때 이용하는 속성이다.

<br/>

**PROPAGATION_REQUIRED**

트랜잭션 정의의 기본 설정 값으로, 일반적인 트랜잭션 동기화 범위를 정의한다.

기존에 트랜잭션이 존재하는 경우 해당 트랜잭션을 사용하며 없는 경우 생성한다.

<br/>

**PROPAGATION_SUPPORTS**

트랜잭션이 존재할 경우 해당 트랜잭션을 이용하고 존재하지 않는 경우 트랜잭션을 사용하지 않는다.

<br/>

**PROPAGATION_MANDATORY**

트랜잭션이 존재할 경우 해당 트랜잭션을 이용하고 존재하지 않는 경우 예외를 발생시킨다.

<br/>

**PROPAGATION_REQUIRES_NEW**

항상 새로운 트랜잭션을 생성하며, 기존 트랜잭션이 있다면 해당 트랜잭션을 중단시킨다.

<br/>

**PROPAGATION_NOT_SUPPORTED**

트랜잭션이 존재할 경우 해당 트랜잭션을 중단시키고 트랜잭션을 사용하지 않는다.

항상 비 트랜잭션 방식으로 작업을 실행한다.

<br/>

**PROPAGATION_NEVER**

트랜잭션이 존재할 경우 예외를 발생시킨다. 

<br/>

**PROPAGATION_NESTED**

트랜잭션이 존재할 경우 중첩된 트랜잭션을 생성하며, 존재하지 않을 경우 REQUIRED와 동일하게 동작한다. (트랜잭션을 생성하여 동작한다.)

**트랜잭션의 생성은 해당 작업(쓰레드)이 connection pool 에서 하나의 connection을 꺼내어 사용함을 의미하며, 기존 트랜잭션의 반환은 동일한 connection을 사용함을 의미한다.**

동일한 범위 내에서 독립적으로 열린 트랜잭션 사이에 데드락이 발생할 수 있으며, 별도의 context를 공유하지 않기에 자원에 대한 비효율이 발생할 수 있다.

---

## 트랜잭션 격리 수준

<br/>


**ISOLATION_DEFAULT**

Database의 기본 격리 수준을 이용한다.

<br/>


**ISOLATION_READ_UNCOMMITTED**

**Dirty Read**, 반복 불가능한 읽기, 팬텀 읽기를 발생시킬 수 있다.

*Same as java.sql.Connection.TRANSACTION_READ_UNCOMMITTED*

<br/>


**ISOLATION_READ_COMMITTED**

**Dirty Read**를 해결했으나 반복 불가능한 읽기, 팬텀 읽기가 발생한다.

다른 트랜잭션이 해당 행을 읽는 것을 금지하나 커밋되지 않은 변경사항이 보일 수 있다.

*same as java.sql.Connection.TRANSACTION_READ_COMMITTED*

<br/>


**ISOLATION_REPEATABLE_READ**

**Dirty Read**와 반복 불가능한 읽기를 해결했으나, 팬텀 읽기가 발생한다.

트랜잭션이 커밋되지 않은 변경 사항이 있는 행을 읽는 것과 다른 행을 읽는 것을 금지한다.

*same as java.sql.Connection.TRANSACTION_REPEATABLE_READ;*

<br/>


**ISOLATION_SERIALIZABLE**

**Dirty Read**, 반복 불가능한 읽기 및 팬텀 읽기를 방지한다.

데이터의 일관성 및 동시성을 위해 MVCC(Multi Version Concurrency Control)을 사용하지 않고 모든 데이터에 대해 lock을 설정한다.

다른 트랜잭션이 모든 행을 읽는 상황을 금지한다.

*same as java.sql.Connection.TRANSACTION_SERIALIZABLE*

---



### **트랜잭션 시간 설정**

<br/>

**TIMEOUT_DEFAULT**

기본 트랜잭션 시스템의 기본 시간 제한을 사용한다.

---

### **트랜잭션 경쟁시 발생하는 문제**

<br/>

**Dirty Read**

트랜잭션 A가 값을 읽기 쓰기할 때 커밋되지 않았음에도 트랜잭션 B가 해당 값들을 조회하는 경우를 말한다.

만약 트랜잭션 A가 롤백된다면 해당 값은 DB에 반영되지 않기에 트랜잭션 B는 잘못된 값을 읽고, 사용하게 된 것이다. 

*한 트랜잭션이 다른 트랜잭션의 작업이 커밋되지 않은 상황에서 접근할 경우 발생하는 데이터 불일치이다.*

<br/>

**Non-Repeatable Read**

트랜잭션 A가 조회 쿼리를 통해 값을 읽고 다시 동일한 조회 쿼리를 실행하기 전 트랜잭션 B가 조회되었던 값을 수정하고 커밋하여서 재실행된 조회 쿼리가 다른 결과를 나타내는 경우를 말한다.

*한 트랜잭션이 같은 쿼리를 두번 실행했을 때 발생할 수 있는 데이터 불일치이다.*

<br/>

**Phantom Read**

트랜잭션 A가 조회 쿼리와 특정 조건을 사용하여 특정 범위의 값을 읽고 다시 동일한 실행하기 전에 트랜잭션 B가 해당 범위에 값을 추가하고 커밋함으로써 실행 결과가 달라지는 경우를 말한다.

*한 트랜잭션이 일정 범위의 레코드를 두번 이상 읽을 때 발생할 수 있는 데이터 불일치이다.*