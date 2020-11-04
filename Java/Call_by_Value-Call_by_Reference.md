2020.11.01

<br/>

## Call by Value, Call by Reference?

<br/>

### **literal? (리터럴)**

**literal** 이란 **소스 코드의 고정된 값을 대표하는 용어이**다. 

정수, 부동소수점 숫자, 문자열, 불린 자료형 등을 이야기한다. ( **즉 Primitive Type 을 말한다.)**

<br/>

### **Call by Value (값에 의한 호출) - Primitive Type**

**Method 호출 시 전달되는 변수의 값(literal)을 복사해서 함수의 인자로 전달한다.**

Method 파라미터를 통해 **내부의 변수에** **literal** 만을 전달하는 것이기 때문에, 내부에서 **지역 변수가** **해당 영역의 Scope**를 가지고 **생성되어서 동작**하고, 지역 변수 값의 변화가 호출했던 대상의 변수에 적용되지 않는다. [ **Stack 영역에서 값을 공유하는 것이 아니고 새로 선언되기 때문에** ]

```java
public class Ex01 {

	public static void swap(int a, int b) {
		
		// Stack 영역에서 swap 함수를 scope를 가진 지역변수 aa, bb가 선언된다.
		int aa = a; // 10
		int bb = b; // 20
		int tmp = 0;
	
		temp = aa;
		aa = bb;
		bb = temp; 
	
		System.out.println(aa); // 20
		System.out.println(bb); // 10
	}

	public static void main(String[] args) {

		int a = 10;
		int b = 20;
		
		System.out.println(a); // 10
		System.out.println(b); // 20
		swap(a, b); 

		System.out.println(a); // 10
		System.out.println(b); // 20

	} 

}
```

<br/>

### **Call by Reference (참조에 의한 호출) - Reference Type**

**Method 호출 시 인자로 전달되는 변수, 인스턴스의 레퍼런스(참조 값, 주소 값)를 전달한다 (C, C++)**

**하지만 Java 에서는 Call by Reference로 동작하지 않는다.  (Java는 모두 Call by Value이다.)**

**주소 값을 직접 접근하는 것**이 아니라, 해당 **Reference Type** 의 **주소 값을 복사**해서 인자로 넘기고  사용한다. 따라서 **원본 객체의 프로퍼티까지는 접근이 가능하나**, **객체 자체를 변경할 수 없다**.            ( **Call by Reference를 흉내 내는 것이다.** )

```java
		public static class User{
            String a;

            public User(String val){
                this.a = val;
            }
    }

	  // **User Type의 a**를 선언하고 Field에 **literal**을 할당한다. 
				User a = new User("lob"); 

  	// **System.identityHashCode(object)** **객체가 메모리에서 가진 해쉬 주소값을 출력**
        System.out.println(System.identityHashCode(a)); 533956291

        paste(a);
    }
		// b는 a의 복사된 주솟값을 가진다
        public static void paste(User b){ // 2번

		// 복사된 주솟값을 가지기 때문에 받은 직후 출력값을 확인하면 동일하다.
            System.out.println(System.identityHashCode(b)); 533956291
						
			// 덮어쓰기를 시도한다.
            b = new User("lob");

			// varlable b가 가리키고 있는 주솟값이 변경되었다.
            System.out.println(System.identityHashCode(b)); 249515830

        }

// C, C++
// 2번 
// varlable      메모리 위치 	   
      a  ---->     "lob"
      b  ---->       a
  
// Java
// varlable      메모리 위치           메모리 위치
//               메모리의 값           메모리의 값
							    	0x01               533956291 
      a  ---->    533956291     -->      "lob"

							    	0x02               533956291 
      b  ---->    533956291     -->      "lob"

// 덮어쓰기를 시도한다.
// varlable      메모리 위치           메모리 위치
//               메모리의 값           메모리의 값  
							    	0x01               533956291 
      a  ---->    533956291     -->      "lob"
								
							    	0x02               249515830 
      b  ---->    249515830     -->      "lob"
//**가리키는 주소값이 변경되진 않는다 해당 주소에 존재하는 값이 바뀔 뿐이다.**
```

Java의 **참조형과 C, C++ 포인터 변수와의 차이점은 주어진 주소를 변경할 수 있느냐이다**.

원래대로라면 참조형 변수를 한번 선언하면 가리키고 있는 기억 장소에 대한 주소를 변경할 수 없다.