2020.11.18

 Mysql 엔진의 잠금

 잠금 - 동시성을 제어하기 위한 기능

- 여러 커넥션에서 동일한 자원(레코드나 테이블) 을 요청한다면 순서대로 한 시점에선 하나의 커넥션만 변경을 할 수 있게끔 하는 것을 말한다.

**Mysql 엔진의 Lock**

**Global Lock - FLUSH TABLES WITH READ LOCK 명령**

- 하나의 세션이 해당 Lock 을 잡는동안 다른 세션은 SELECT를 제외한 DDL, DML에 대하여서 Lock을 선점하기 전까지 대기 상태로 남는다. (Mysql 서버의 전 영역)
- MyISAM이나 MEMORY TABLE에 대한 일관적인 백업(mysqldump)을 받아야할 때는 해당 Lock을 사용한다.

**Table Lock - LOCK TABLES table_name [ READ  | WRITE ] 명령**

- 명령을 통해 특정 테이블에 Lock 을 설정할 수 있다. (MyISAM, InnoDB 등에도 가능)
- 묵시적으로도 가능하며 명시적으로 획득한 Lock은 UNLOCK TABLES 으로 반납해야 한다.
- 명시적인 Lock은 특별한 상황이 아니면 사용할 일이 없다.
- 묵시적인 Lock 은 MyISAM, MEMORY TABLE의 데이터를 변경하는 쿼리에 의해 발생한다.
- 서버가 해당 TABLE에 Lock 설정 → 데이터 변경 → 잠금 해제

- InnoDB의 경우 스토리지 엔진에서 레코드 기반의 잠금을 지원하기에 DDL이 아닌이상 묵시적인 Lock이 설정되지 않는다

**USER Lock - GET_LOCK() 함수**

- 사용자가 지정한 문자열에 대해 Lock을 획득하고 반납하는 것을 말한다.
- 여러 클라이언트의 상호 동기화를 처리해야하는 경우 사용할 수 있다.
- 많은 레코드를 한번에 변경하는 트랜잭션의 경우에도 유용하게 사용할 수 있다.
    - 쉽게 Dead Lock 문제를 해결할 수 있다.