2020.12.03

<br/>

Mybatis의 두가지 사용 방식

<br/>

마이바티스를 적용하기 전에 관련 글, 영상을 찾아보니 사용하는 방법이 크게 두가지로 나뉘는 것을 알 수 있었다.

어떠한 방법을 적용하는 것이 생산성을 올릴 수 있을까?

- **Dao Interface → Mapper XML 연동 ( mapper namespace = package.xxMapper)**
    - 예시

        ```java
        @Repository
        public interface PostMapper {

        		List<String> someMethod(Type param1, Type param2);

        }

        -------------------------------

        public class PostDto {

        		private Long postId;
        		private User user;
        		private Location location;

        }

        -------------------------------

        <mapper namespace="com.post.mapper.PostMapper">
        		<resultMap type="com.post.dto.CreatePostDTO" id="postInfo">
        				<result column="postId" property="postId"/>
        				<association property="user">
        					<result column="userId" property="userId">
        					......
        				</association>
        				<association property="location">
        					<result column="city" property="city">
        					......
        				</association>
        		</resultMap>

        <select id="someMethod" resultMap="someMethodInfo">
        		SELECT ......
        					 ......
        		FROM   ......
        					 ......
        		WHERE  ......
                   ......
        </select>
        ```

<br/>

- **Dao Interface method 에 Annotation 작성하여 사용.**
    - 예시

        ```java
        @Mapper
        public interface PostMapper {
        		
        		@Select("Select * From TableName")
        		@Results({
        				@Result(column="postId" , property="postId"),
        				........
        		})
        		List<String> someMethod();
        		
        		@Insert("Insert INTO post(post_title, post_content) VALUES(#{company.title), #{company,address}}"))")		
        		@Options(useGeneratedKeys=true, keyProperty="postId")
        		int insert(@Param("post") Post post);
        }
        ```

        - @Results = <resultMap>
        - @Result = <result>
        - @One = <association>
        - @Many = <collection>
        - @Insert, Update, Delete, Select → Insert, update, delete, select
        - @Param

<br/>

**두 방식의 장단점?**

<br/>

**XML 방식** 

장점

- Nested Join Mapping 이 가능하다.

    → 다양한 연관관계 매핑, Join 문을 지원한다.

- resultMap의 xml 방식은 상속을 제공한다. (같은 속성을 재사용할 수 있다.)
- if, choose, when, trim, where, foreach 등 다양한 기능을 쿼리에서 사용할 수 있다.

단점

- 복잡한 xml 문법과 설정의 번거로움이 있다.

<br/>

**Annotation 방식**

장점

- 간단한 기능의 경우 Annotation을 사용하는 것이 쉽게 적용 가능하고, 가독성 등이 좋다.

**단점**

- 쿼리의 구성이나, **Result 구성(매핑되는 속성)**이 같더라도 재사용을 하지 못하고 복사하여야 한다.
- 복잡한 기능, 동적 쿼리에 대하여서 지원하지 못하거나 큰 불편함을 가진다.

**결론**

- Select all, Create post, Insert Post 등 간단한 쿼리는 주석을 사용하는 것이 좋고 복잡한 Join (연관 관계 매핑), 동적 쿼리에 대하여선 xml을 사용하는 것이 좋다.
- 한가지 방법만 사용하는 것보단 상황에 맞게 사용하는 것이 더 좋다.