Spring에서 지원하는 DI 패턴으로는

- Constructor Injection
- Setter Injection
- Method Injection
- Field Injection

등이 존재한다.

<br/>

### **Constructor Injection**

생성자 주입의 동작

1. Bean을 등록하기 위한 Component Scan 중에 해당 Class를 확인하게 된다. 
2. 해당 클래스의 @Autowired가 붙은 생성자를 확인하고 인스턴스를 생성한다.

<br/>

**특징**

- 객체 생성 시 호출되는 생성자를 통해 1번만 주입되는 것을 보장한다.

  **불변적이며, 우선적으로 주입된다.**

- Spring에서는 필수적인 의존성 주입에 대하여서 생성자 주입을 권장한다.

```java
@Service
public class UserService {

		private final UserRepository userRepository;

		// 해당 클래스가 다른 생성자를 가지지 않는다면 생략할 수 있다. Spring 4.3+ 
		// 그리고 추가적으로 Configuration 클래스에도 생성자 주입을 할 수 있게 되었다. 
		@Autowired
    public UserAccountService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

		// Logic...

}
```

<br/>

**장점**

- **단위 테스트가 용이하다.**

    Bean을 생성하는 동안 다른 Bean을 초기화할 수 있다. (별도의 값을 저장한다던지..)

- **클래스 래벨에서 불변한 객체 주입이 가능하다.**

    final Keyword와 생성 시점의 주입을 통해 갑작스러운 변경에 의한 문제를 해결하게 된다.

    만약에 주입 대상이 되는 Bean들이 없거나, 오류로 인해 주입되지 않을 경우 Checked Exception을 통하여 알아차릴 수 있다. (NPE를 방지한다.)

- **쉽게 불변 객체를 만들어낼 수 있다.**

    **안정적인 상태를 유지할 수 있다. → 모든 의존성이 주입되거나 인스턴스화를 실패한다.**

- **리플랙션 API 를 사용하지 않는다. (주입 시에 Spring 상에서 최소의 자원을 사용한다. )**
- **생성자를 사용하여 인스턴스를 생성하는 것이 OOP 관점에서 자연스럽다.**

<br/>

**단점**

- **해당 Bean이 선택적인 종속성을 가져야하는 경우 (가변적인 상태여야 하는 경우)**

    **Setter Injection을 사용하자. (생성자 주입과 설정자 주입은 결합이 가능하다.)**

<br/>

**단점이라기엔 오묘한..? ( Ugly Constructor Problem)**

- **해당 Bean이 적은 가짓수의 의존성을 가질 경우**

    해당 Bean의 필수 의존성이 적을 경우 생성자 주입 코드는 **"장황해보일 수도"** 있다.

- **해당 Bean이 아주 많은 가짓수의 의존성을 가질 경우**

    종속성이 추가될 때마다 생성자의 매개 변수에 추가해야함으로 생성자를 통해 과도한   결합을 노출하게 된다. 

<br/>

**추악한 생성자? ( Ugly Constructor? )**

클래스가 수많은 필드(의존성)을 가지고 작성되어 있다면, 생성자도 복잡해질 수 밖에 없다.

```java
// 점층적 생성자 패턴
public class Employee {

        // 필수적인 속성, 의존성
        private final String firstname;
        private final String lastname;

        // 선택적인 속성, 의존성
        private String email;
        private String address;
        private String phone;
        
        // Default Constructor 는 필수적인 의존성(final) 을 포함하여야한다. 
        public Employee(String firstname, String lastname) {
            this.firstname = firstname;
            this.lastname = lastname;
        }

				public Employee(String email, String address, String phone) {
            this.email = email;
            this.address = address;
            this.phone = phone;
        }

        public Employee(String firstname, String lastname, String email, String address, String phone) {
            this.firstname = firstname;
            this.lastname = lastname;
            this.email = email;
            this.address = address;
            this.phone = phone;
        }
}

```

사실 가정하는 상황에서는 이것보다 더 많은 인자를 가지고 있을 것이다. 그럴 경우에는 생성자가 더욱 복잡해질 것이고, 객체의 코드를 작성하거나 파악하는 것에 어려움을 겪을 것이다.

Spring의 경우에는 수많은 Service 혹은 Repository 의 생성자 주입을 말할 수 있다.

사실 이러한(추악한) 생성자가 만들어진다는 것은 그 객체의 책임이 혼재되어 있거나 과중한 책임을 지니고 있음을 의미하는 것이다.
**그렇기에 이것은 생성자 주입의 문제로만 치부하기보다는 설계의 문제임을 생각하는 것이 좋다고 한다.**

<br/>

### **Setter Injection**

**수정자 주입의 동작**

1. 해당 클래스를 인스턴스화하기 위해 비어있는 생성자나 정적팩토리 메서드를 호출한다.

    **(생성자 주입이 있는 경우 : 필수인자 생성자를 호출하여 생성한다.)**

2. 해당 인스턴스가 생성된 후 @Autowired 가 붙은 Setter를 호출하여 Bean을 주입한다. 

**(Method Injection도 비슷하게 동작한다.)**

<br/>

**특징**

1. 자바 빈 규약(setter) 을 이용한 의존성 주입 방식이다.
2. Spring에서는 변경 가능성 있는, 선택적인 종속성에 대해서 설정자 주입을 권장한다.

```java
@Service
public class UserService {

		private final UserRepository userRepository;

		// 특별한 요청에 경우 필요하다고 가정. 
		private GuestRepository guestRepository;

    public UserAccountService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
		
		@Autowired
		public void setGuestRepository(GuestRepository guestRepository) {
				this.guestRepository = guestRepository;
		}

		// Logic...

}
```

**장점**

- 선택적인 종속성에 대하여서 별도의 설정자를 만들고 유연한 제공이 가능하다.
- **리플랙션 API 를 사용하지 않는다.**

<br/>

**단점**

- 의존성이 주입되는 시점이 많아질 수 있기에 가독성이 떨어지게 되고, 상대적으로 유지보수 능력을 떨어트린다.
- 설정자가 호출될 때마다 객체가 새로 주입됨으로 **참조에 대한 불변을 유지할 수 없다.**

    **Null이 주입될 수 있고, 새로운 객체의 반환으로 기존 작업 값을 잃을 수 있다.**

- 설정자를 통해 클래스 내부의 의존성 부분을 노출함으로써 캡슐화를 위반하게 된다.
- 객체 생성 시점에는 순환 종속성이 발견되지 않으나, 비즈니스 로직이 진행되는 도중에 해당 문제가 발생할 수 있다.

<br/>

### **Field Injection**

1. 클래스를 인스턴스화하기 위해 비어있는 생성자나 정적 펙토리 메서드를 호출한다.
2. 필드 주입을 위해 Reflection API 를 이용하여 클래스를 

```java
@Service
public class UserService {

		// 간단한 주입방식!
		@Autowired private UserRepository userRepository;
		

		// Logic...

}
```

**장점**

- 모든 주입 방식 중 가독성 측면에서 가장 간결하게 사용할 수 있다.

    필드 주입을 선택하는 이유 중 하나는 보일러플레이트 코드 (여기서는 생성자와 수정자) 를 작성하지 않기 위함이다.

- 많은 필드를 갖는 것이 불가피하고 추악한 생성자가 문제가 되는 상황에서는 필드 주입을 사용하는 것이 **"나을 수도"** 있다. (별도의 Null 체크가 필요..?)

    **그런 상황에서 필드 주입을 선택하는 것이 최선인지는 의문이 든다.**

<br/>

**사용 할 것이라면..**

- Application 코드와 관계 없는 테스트 코드에 사용 (Mock 객체 주입 등)
- 스프링 설정을 목적으로 하는 @Configuration 클래스에서만 사용

<br/>

**단점**

- Private한 필드에 의존성을 주입할 수 있는 유일한 방법은 스프링 컨테이너가 클래스를   **인스턴스화하고 Reflection API 를 사용하여 주입하는 것이다.**

    **이러한 주입 방식은 생성자, 설정자 주입 방식보다 많은 자원을 소모한다.**

- Spring 의 DI에 의존적인 코드를 작성하게 된다. (순수한 자바 코드가 아니다.)

    **이 코드가 POJO를 위반하는 것일까?** 

- 의존성 주입을 실패하게 된 경우 (Bean을 찾지 못하였거나, 별도의 처리 없이 여러개인 경우) Exception이 터지는 것이 아니라 null 이 주입되게 되고 비즈니스 로직시에 문제가 발견될 수 있다