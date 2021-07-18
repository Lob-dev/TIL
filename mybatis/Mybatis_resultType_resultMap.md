2021.01.18

### **resultType**

쿼리의 결과를 반환할 때 제공 타입을 정의한다. *string, long, User(entity) 등*

Entity 객체를 사용하는 경우에는 필드명이 반환될 객체 내부에 동일한 이름을 가져야한다.

아니면 쿼리문에서 별칭을 정의함으로써 매핑시킬 수 있다. *as "{field name}"*

*Value Object를 결과로 사용할 경우 myBatis는 반환되는 필드명을 기준으로 resultMap을 생성하고 entity와 매핑한다.*

```jsx
public class User {
		private Long id;
		private String password;
		//......
}

<select id="findAll" resultType="...User" >
    SELECT 
				user_id as id,
				password
		FROM user
</select>

// 이런 resultMap이 생성된다.

<resultMap id="userResultMap" type="User">
  <id property="id" column="id" />
  <result property="password" column="password"/>
</resultMap>
```

*Collection을 사용하여 여러개의 결과값을 전달할 때에도 객체만을 명시하면 된다.*

<br/>

### **resultMap**

쿼리의 결과를 반환할 때 제공 될 객체와 필드 간의 연관 관계를 정의한다. *User(entity)*

필드 명이 동일하지 않더라도 결과 값이 필드와 매칭되어 있기에 문제없이 전달 가능하다.

```jsx
public class User {
		private Long user_id;
		private String password;
		//......
}

<resultMap id="userMap" type="User">
  <id property="id" column="user_id" />
  <result property="password" column="password"/>
</resultMap>

<select id="findAll" resultMap="userMap">
	  SELECT 
				user_id,
				password
		FROM user
</select>
```

**참고 자료**

- [https://mybatis.org/mybatis-3/ko/sqlmap-xml.html#Result_Maps](https://mybatis.org/mybatis-3/ko/sqlmap-xml.html#Result_Maps)