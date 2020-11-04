2020.10.01

**Casting** : 형 변환, 원시 타입의 변환부터 상속관계의 상위, 하위 간의 형변환을 통칭한다.

**Up Casting** :  하위 클래스 객체를 상위 클래스의 타입으로 해석(형변환) 하는 것. ( 다양한 자식 객체를 부모의 타입으로 관리할 수 있다. )

**Down Casting** : Up Casting 된 상태의 객체를 다시 원 상태(하위 클래스)로 돌리는 것.

```java
public class Casting {
    public static void main(String[] args) {
				// Up Casting
        ApplicationContext ac = 
                     new AnnotationConfigApplicationContext(..class);

				// Down Casting
        AnnotationConfigApplicationContext AnnotationAc = 
										(AnnotationConfigApplicationContext) ac;
    }
}
```

```java
public class Casting {
    public static void main(String[] args) {
				
        // Up Casting
        // 상위 클래스의 공통 메서드, 상태는 하위 클래스에서 사용 가능하다.
        // 하위 클래스의 재정의, 고유 메서드, 상태는 상위 클래스에서 
        // 사용이 불가능하다.
        Parent childOne = new Child();
        childOne.name = "name";
        childOne.getDegree(); // Child의 클래스 예외 발생

        // Down Casting
        Child childTwo = (Parent) childOne;
        
        childTwo.getDegree(); // 사용 가능

    }
}
```