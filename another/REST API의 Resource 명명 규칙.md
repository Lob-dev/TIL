2020.12.03

### REST API의 Resource 명명 규칙


제공되는 정보에 대한 이름을 기준으로 작성한다. (post, customer...)

<br/>

단일 자원과 복수(LIst) 자원에 대한 별도의 Path를 생성하여야 한다.

```java
/post  -->  하나의 post 를 제공받는 URL
/posts -->  여러 post 를 제공받는 URL

/customer
/customers
...
```

<br/>

**Resource가 보유하고 있는 하위 Resource 에 대해서 이러한 Path를 작성할 수 있다.**

```java
( 하나의 Customer 정보 )의 accounts (들)
/customers/{customerId}/accounts --> cusomer가 가지고 있는 accounts 정보들

( 하나의 Customer 정보 )의( 하나의 accounts  )
/customers/{customerId}/accounts/{accountsId}
```

<br/>

추가적으로

- **자원을 나타내는 명사만을 사용하여야 하며, 동사를 사용하지 않는다.**
- **/ 를 통하여 계층 관계를 나타내어야 한다. (맨 뒤에는 / 를 사용하지 않는다.)**
- **명사와 명사 사이에는 '-' (하이픈) 문자을 사용한다.**
    - 가독성 향상을 위하여 사용한다.

    → inventory-management...

- '**_' 을 사용하지 않는다.**
    - 일부 브라우저나 화면에서 _ 은 보이지 않거나 가려질 수 있다. (혼동을 줄 수 있다.)
- **소문자 만을 일관되게 사용하여야 한다.**
- **파일 확장자를 사용하지 말아야한다. (.do, .xml, .html...)**
    - 파일의 확장자는 어떠한 이점을 주지 않으며 (필요치 않으며), URL의 길이만 늘린다.
- **CRUD 함수 명을 사용하지 않는다.**
    - URI 는 리소스를 식별하는데 사용하는 것이지, 작업을 나타내는 용도가 아니다.
    - HTTP Method 를 사용하여 수행되는 CRUD 기능을 표시하여야 한다.