2020.11.09

<br/>

**Java의 synchronized Lock 범위**
- Java의 synchronized 범위는 Object 단위의 단일 개체 잠금이다.
    - 모든 Object는 하나의 모니터를 지니고 있다.
- 하나의 쓰레드가 Monitor의 Lock을 획득하면 내부의 다른 임계영역에도 접근할 수 있다.
- 쓰레드가 객체 내부의 임계영역에 접근할 경우, Lock 을 통해서 접근하게 되며, 다른 쓰레드는   해당 쓰레드가 임계영역에서 나올 때까지 Wait Queue 에서 대기하여야 한다.

<br/>

**synchronized Method**

- 해당 Method를 사용하기 위해서는 우선적으로 Lock을 획득하여야 한다.
- 해당 Method가 Instance Method인 경우 Method를 호출하는 객체에 대하여서 Lock을 획득한다.

    어떠한 쓰레드가 해당 객체의 method 단위의 임계영역을 접근한다면, 이 객체의 다른 메서드를 사용하는 쓰레드들도 모두 Block 상태가 된다.

    ```jsx
    class b {

    A a = new A();
    	a.someMethod(); // instance a 에 대한 Lock을 획득하고 호출한다.

    }

    class A {
    	public synchronized void someMethod() {
            // Do SomeThing
      }
    	
    }
    ```

- Class (static) Method인 경우 해당 method가 속해진 Class Instance에 대한 Lock을 획득한다.

    즉 해당 class insatance가 여러개가 존재하더라도, 해당 메서드에 대해 동시 접근이 불가능하다.

    왜냐하면 Class (Static) Object는 Heap 영역에 하나만 존재하기 때문이다.

    ```jsx
    class B {
    	A.someMethodIsStatic(); // class A 에 대한 Lock을 획득하고 호출한다.
    }

    class A {
    	public synchronized static void someMethodIsStatic() {
            // Do SomeThing
      }

    	public synchronized void someMethod() {
            // Do SomeThing
      }
    }
    ```



**synchronized Statement**

- **synchronized Statement** 는 런타임 중에 분기 등에서 필요한 경우에만 Lock을 획득하게 된다.
- 상대적으로 더 작은 범위를 임계영역으로 설정하기 때문에 성능상 조금의 이점이 있을 수 있다.