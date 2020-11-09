2020.11.09

**Object wait, notify** 

<br/>

  **wait**
   - **해당 Object를 선점하고 있다가 접근 권한을 다른 쓰레드에게 넘기고 notify, notifyAll 이 호출될 때까지 해당 쓰레드를 대기하게끔 설정하는 메서드이다.**
        - **Wait Set에 들어가게 된다.**
        - 추가 : Block 은 아직 접근조차 하지 못한 상태를 말하는 것이다.

<br/>

  **notify**

  - 해당 Object의 선점을 위해 Wait Set 에 대기중인 하나의 쓰레드를 실행 대기 상태로 만든다.
        - Entry Set의 쓰레드들과 같이 경합에 참여한다.

<br/>

  **notifyAll**

  - 해당 Obejct의 선점을 위해 Wait Set 에 대기중인 모든 쓰레드를 실행 대기 상태로 만든다.
        - notify와 동일한 경합상태가 발생한다.

<br/>

  **Object 선점 흐름**

  - **Object에 대한 Thread 초기 접근.**
        - Thread → Entry set → Monitor Lock 획득 시도
        - 선점 Thread Wait() → notify, notifyAll → 대기중인 Thread 중 Monitor Lock 획득
  - **Object에 대한 Thread 접근 (Wait 이후)**
        - Wait set → notify, notifyAll → 대기중인 Thread 중 Monitor Lock 획득

<br/>

  Wait Set의 Thread가 임계영역을 벗어나기 위해서는 접근 권한을 선점하고 Lock을 놓는 방법 뿐이다.