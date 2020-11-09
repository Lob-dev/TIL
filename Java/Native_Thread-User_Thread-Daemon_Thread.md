2020.11.09

**커널 래벨 (네이티브) 스레드란?**

- 커널을 통해 관리되는 커널 종속적인 쓰레드이다.
- 어플리케이션 프로세스의 첫 번째 (Main) 쓰레드는 커널 쓰레드이다.
- 프로그래머의 요청에 따라 쓰레드를 생성하고 해당 쓰레드가 커널을 통해 스케줄링 된다면, 커널 레벨 쓰레드라고 한다.

<br/>

**커널 래벨 (네이티브) 쓰레드의 장점**

- 하나의 프로세서 안에 스레드들을 몇몇 프로세서에 한꺼번에 디스패치하는 것이 가능함으로 멀티 프로세스 환경에서 매우 빠르게 동작한다.
    - 디스패치? : 준비 상태에 있던 쓰레드가 CPU에 할당받아 실행되는 것을 말한다.
- 다른 쓰레드가 입출력 작업이 끝날 때까지 다른 쓰레드를 사용하여 작업을 진행할 수 있다.
- 커널이 각 쓰레드를 개별적으로 관리할 수 있다.

<br/>

**커널 래벨 (네이티브) 쓰레드의 단점**

- 쓰레드의 관리를 위하여 커널을 호출하는데 일정 시간이 소비된다.
- 쓰레드간의 동기화 문제가 발생할 수 있으며, 그에 따른 처리가 복잡하다.
- 사용자 쓰레드에 비하여 자원을 더 많이 소비한다.
- 쓰레드 작업 시에 Context Switching 이 발생한다.

<br/>

**사용자 (그린) 쓰레드란?**

- 사용자 영역에서 연산을 수행한다.
- 프로세스 내부에 존재하는 **Main 쓰레드를 통해 생성**되며, Context Switching 을 하지 않기에 커널 쓰레드보다 오버헤드가 적다.
    - **Main Thread : Main Method를 실행하는 쓰레드, JVM이 Application 구동을 위해 사용.**

<br/>

**사용자 쓰레드의 단점**
- 프로세스 내의 하나의 쓰레드가 커널로 진입하게 된다면, 모든 쓰레드가 Blocking 된다.
- 사용자 쓰레드는 커널을 통해 스케줄링이 되지 않기에, 각 CPU에 효율적으로 쓰레드를 분배할 수 없다.

<br/>

**데몬 쓰레드란?**

- (프로세스의) 메인 쓰레드의 작업을 돕는 보조적인 역활을 하는 쓰레드를 말한다.
- JVM의 Main 쓰레드의 작업이 종료된다면, 데몬 쓰레드는 동작을 중지한다.
- 쓰레드 생성시에 SetDaemon() 메서드를 이용하여 설정할 수 있다.

<br/>

**JVM에서의 데몬 쓰레드?**

- VM Background Thread : Compile, Optimization, GC를 수행하는 Background 데몬 쓰레드.
- 기타 User Thread :  thread.SetDaemon()