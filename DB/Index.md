2021.01.22

### **index?**

---

책의 목차, 색인과 같은 역할을 담당하는 데이터베이스 객체로써 테이블과 독립적으로 존재한다. 하지만 테이블에 의존적이기에 해당 테이블이 삭제될 경우 같이 제거된다.

존재하는 컬럼의 값과 해당 레코드가 저장된 주소를 키와 값의 구조로 묶어 저장하고, 정렬된 상태를 유지하기에 저장, 수정 기능의 성능을 희생하고 빠른 조회를 제공하는 것이 목적이다

<br/>

### **기본 제공되는 Index?**

---

기본적으로 제공되는 Index는 PK index이며, 일례로 InnoDB는 설계상 이유 때문에 모든 테이블에 PK가 필요하기에, 개발자의 설계시 PK를 작성하지 않는다면, 암시적으로 PK를 생성하여 레코드를 탐색하는데 사용한다.

### Primary Key vs Secondary Key

---

- **Primary Key** : 테이블마다 기본 키 제약 조건을 통해 만들어지는 하나의 고유한 Key를 의미한다. Null 값과 중복 값을 허용하지 않으며, 앞서 이야기한 것처럼 자동으로 인덱스를 생성하여 데이터에 대한 빠른 접근을 지원한다.
- **Secondary Key** :  각 레코드에 대한 고유한 값을 제공하는 Key를 의미한다. Null을 허용하며, 레코드를 식별하는데 사용할 수 있고 Index로 활용할 수 있다. 하나의 테이블에 여러 Key가 존재할 수 있다.

<br/>

### **Unique Index를 사용하는 이유?**

---

Index에도 Unique Index와 Non-Unique Index가 존재한다. 간단하게 Unique Index를 알아봄으로써 어떤 유의미한 차이가 있는지 확인해보자.

- **Unique Index** : **하나의 Key만 존재함을 나타낸다.** 이는 Index에 대해 동등 조건을 사용하는 쿼리에서 DB의 쿼리를 실행하고 최적화하는 옵티마이저에게 하나의 Index를 찾았을 때 더이상 스캔하지 않아도 된다는 의미를 제공하게 되며, 이를 통해 쿼리 최적화를 수행한다. **그렇기에 사용 가능한 모든 경우에서는 Unique Index를 사용하는 것이 권장된다.**

    *추가적으로 고유한 인덱스를 사용하는 것은 해당 레코드의 데이터 무결성을 보장한다.*

<br/>

### **index의 장점?**

---

- **조회(SELECT) 쿼리의 성능을 향상시킨다.** 대부분의 서비스에선 데이터를 조회하여 사용자에게 제공하는 것이 다른 쿼리보다 많은 비중을 차지하기 때문에 도입하게 된다.
- **고유한 index 형식을 사용한다면 이는 행에 대해서도 중복없이 구성하는 것을 보장한다.**
- **매번 테이블을 스캔한 후 행을 정렬하는 절차를 생략하게 한다.** 미리 정렬된 목록을 제공함으로써 매 쿼리마다 정렬을 하지 않아도 빠르게 데이터를 검색할 수 있도록 지원한다.

    *DB는 모든 행을 스캔하고 정렬한 뒤 일치하는 행을 필터링하여 결과를 반환한다.*

<br/>

### **index의 단점?**

---

- **데이터의 수정(insert, update, delete)이 발생할 때마다 연관 index도 업데이트해야 한다.** 즉 다른 쿼리의 성능이 떨어지게 된다. *하지만, update의 경우 where 조건에 index를 사용하고 있다면 해당 컬럼을 찾아 변경하는 성능을 높일 수 있다. (index update는 동일하다.)*
- **Index는 DB 내에서 별도의 저장 공간을 차지한다.**
- **Index를 유지하는 비용이 발생한다.** 대표적으로 데이터 수정 ,삭제, 추가 등의 경우 Index가 업데이트되다가 깨지는 경우가 발생하게 되는데 이때 복구, 정상적인 Index로의 우회 등 추가적인 처리가 필요하다.

<br/>

### **Index Corruption?**

---

Corrupt Index는 DDL, DML(insert, update, delete) 쿼리를 수행한 뒤 정렬하는 도중이나, Slow Query에 의한 비정상적인 종료에 의해 발생할 수 있다. 하지만 확실하게 손상되었는지 확인할 수 없기 때문에, 이를 파악하기 위해서는 실행시간과 결과를 관찰하여야 한다.

<br/>

### **index를 사용하여야하는 시점?**

---

테이블에 인덱스를 추가하는 것은 저장 속도를 어디까지 희생하고, 읽기 속도를 얼마나 더 빠르게 만들어야 하는지의 여부에 따라 결정되어야 한다.

**인덱스를 추가하거나 제거할 때마다 성능 테스트를 수행하여 어떤 영향이 미치는지 실질적인 수치를 파악하여야 한다. 이는 많은 검색 쿼리에서 빈번하게 키로 사용되는 열에 대해서 인덱스를 사용한다는 것이 기본 전제이며, 이를 정량화하여 판단해야 한다는 것이다.**

<br/>

### **index의 알고리즘?**

---

간단하게 Index 알고리즘의 종류와 개념만 작성하였다.

- **B-Tree Index :** balanced Tree를 사용하는 방식을 의미하며, 일반적으로 사용되는 유형이다. 컬럼의 값을 변형하지 않고, 원래의 값만을 이용해 Indexing 하는 특성을 가진다.

    *여러 형태의 변형된 알고리즘을 가진다. B+-Tree, B*-Tree 등*

- **Hash Index :** 컬럼의 값을 Hash 값으로 계산하여 Indexing 하는 방식이다. 매우 빠른 검색을 지원하나 일부 값을 통해 값을 찾는 Pattern matching을 지원하지 못한다. 주로 메모리 기반의 DB에서 사용된다. ****
- **Fractal-Tree Index :** B-Tree 방식의 단점을 보완하기 위해 고안된 방식이다. 값을 변경하지 않고 인덱싱 하는 것은 동일하나 데이터가 저장되거나 삭제될 때 발생하는 비용 (Disk I/O)을 줄이도록 설계된 것이 특징이다. 각 내부 노드에 버퍼를 포함함으로써 데이터를 임시로 저장하고 버퍼가 가득 채워졌을 때 Flush 하게 됨으로 I/O 작업 단위를 좀더 크게 유지한다.
- **R-Tree Index :** 2차원 데이터를 Indexing하고 검색하는 목적을 지니는 방식이다. 즉 공간 개념 값을 사용하는 GPS나 GIS 서비스에서 사용하게 된다.
- **Full Text Search Index :** 문서의 내용 전체를 Indexing하여 특정 키워드가 포함된 문서를 분석, 검색하는 방식에서는 B-Tree 형식을 사용할 수 없기에 사용되는 방식이다.

    *크게 Stopword 방식과 N-Gram 방식으로 이야기할 수 있다.*

<br/>

**자료 출처**

- [https://en.wikipedia.org/wiki/Database_index#:~:text=Indexes are used to quickly,efficient access of ordered records](https://en.wikipedia.org/wiki/Database_index#:~:text=Indexes%20are%20used%20to%20quickly,efficient%20access%20of%20ordered%20records).
- [https://www.freecodecamp.org/news/database-indexing-at-a-glance-bb50809d48bd/](https://www.freecodecamp.org/news/database-indexing-at-a-glance-bb50809d48bd/)
- [https://www.enterprisedb.com/blog/how-to-fix-postgresql-index-corruption-deal-repair-rebuild](https://www.enterprisedb.com/blog/how-to-fix-postgresql-index-corruption-deal-repair-rebuild)
- RealMysql