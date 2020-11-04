2020.10.29

<br/>

deque의 구현체?

<br/>

  + ArrayDeque 
  + Abstraction Collection을 상속하고 Deque 인터페이스를 구현한 가변 배열 구현체**
    - **MAX_ARRAY_SIZE는 Integer.MAX_VALUE - 8**
    - **Capacity 가변 값은 Old Capacity 의 50% 이다. ( 16 → 24 → 36 → 54... )**

<br/>

  + LinkedBlockingDeque 
  + Linkded node로 연결된 Blocking Deque 이다.**
    - **Queue가 꽉찼을때의 입력 시도 / Queue가 비어있을때의 출력 시도를 막는다.**
    - **Monitor 상태를 제공하는 Condition 객체 타입을 사용한다.**
        - lock.newCondition을 통해 Condition 인스턴스를 반환받는다.
        - **( 내부적으로 Object의 wait, notify, and notifyAll 를 사용한다. )**
            - 작업 대기 시 wait() 을 통해서 wait Queue 에 있는 다음 쓰레드에게 제어권을 넘긴다.
            - notify() 를 통해 다시 모니터를 선점하고 이전 처리를 이어가며 자원을 사용한다.

<br/>

  + ConcurrentLinkedQueue 
  + 멀티스레드 환경에서 컬렉션의 요소를 동시적으로 처리가능한 메서드.
    - 여러개의 쓰레드가 하나의 데이터에 접근하더라도 동일한 결과를 나타낼 수 있다. ( poll() )
    - VarHandle 을 통해서

<br/>

  + LinkedList