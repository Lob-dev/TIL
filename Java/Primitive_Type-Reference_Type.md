2020.10.03

### **Primitive Type과 Reference Type? (원시 타입과, 참조 타입 )**
<br/>

**Primitive Type**이란 8가지의 기본형를 말합니다. 논리형 boolean, 정수형 byte, short, int, long

실수형 float, double, 문자형 char를 가지고 있습니다. 기본 형은 비 객체이기 때문에 값에 Null을 넣을 수 없습니다.

**Primitive Type은 컴파일된 바이트 코드를 보면 각 값의 크기에 따라 Operand Stack의 각 인덱스에 변수를 저장하고, 해당 변수 값을 꺼내어 사용하거나 상수풀에 저장하여 사용함을 알 수 있습니다.**

**Reference Type**이란 8가지의 기본형을 제외한 모든 타입을 말합니다. 빈 객체를 의미하는 Null 이란 것이 존재하며, 값이 저장되는 곳의 주소 값을 Heap 영역에 저장합니다.

Array, Enum, Class, Interface, String Class(기본형 처럼 사용), Wrapper Class (기본형을 감싸주는) 가 있습니다.

<br/>

**Wrapper Class (기본형과 대응 클래스)**

byte Byte	        short Short	        int Integer	long Long

float Float	double Double	char Char 	boolean Boolean

**Wrapper Class로 기본형을 감싼다면 Null 값도 넣고 전달**할 수 있으며,

이것을 이용하여, 기본 타입의 **데이터를 객체로 표현**합니다. 이 객체를 포장 객체라고 합니다.

**래퍼 클래스의 동작**을 **박싱(감쌀 때)**과 **언박싱(포장 객체 값을 얻어낼 때)**이라고 합니다.

<br/>

### **동일성과 동등성 비교?**

**동일성 비교 ( == )**

해당 인스턴스가 비교하는 인스턴스와 실제로 같은 인스턴스인지 비교하는 것을 말한다. Primitive Type 은 값은 값일 경우 같은 주소를 가진다. 
== 연산자를 통하여서 주솟값을 비교한다. 
+ 스택 영역 내부에서의 리터럴은 일정 값 이하는 Operend Stack에서, 이상은 Constant Pool에서 저장되고 그 것을 꺼내어서 사용하기에 같을 수 밖에 없다.

```java
int a = 10;
int b = 10;

// 동일성 비교는 원시 값만 비교하는 것이 옳다. 
System.out.println(a==b); // true
```

**동등성 비교 ( 재정의한 a.equals(b) )** 

Reference Type 은 각 객체가 같은 값을 가지더라도 주소가 다르기에 동일성 비교로는 확인할 수 없다. 이 경우 equals() 메서드를 통한 값에 의한 동등성 비교만이 정상적인 확인이 가능하다.
어떠한 값을 비교할지 설정하기 위해 equals 를 재정의 해야한다.
+ 기본적으로 equals 도 (this == obj) 주솟 값을 통한 검증을 지원한다.

```java
String str1 = "ABC";
String str2 = new String("ABC");

// false 를 반환한다. 왜? str1과 str2는 다른 인스턴스이기 때문
System.out.println(str1 == str2);

// true 를 반환한다. 왜? 다른 인스턴스 이지만 가지고 있는 값은 같기 때문
// 재정의한 equals
System.out.println(str1.equals(str2));

```

