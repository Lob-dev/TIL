2020.11.03

<br/>

Callable, Future, CompletableFuture

<br/>

**Callable** 

- Runnable과 거의 유사하게 작동하나 어떠한 Type 값을 반환한다.
- call Method 실행 중에 Exception을 반환할 수 있다.

<br/>

**Future**

- 비동기적인 작업의 현재 상태를 조회하거나 결과를 가져올 수 있다.
- 연산 완료 확인, 완료시 까지 대기, 연산 결과 조회 기능을 제공한다.

**Future 관련 메서드**

- **get()** : Blocking Call , Time Out 을 설정 가능하다.
- **isDone()** : Task 를 완료 했으면 true 아니면 false를 리턴한다.
- **cancel()** : Task 을 취소하였으면 true 못하였으면 false 를 리턴한다.
    - 인자에 true를 전달하면 현재 쓰레드를 interrupt 하고 그러지 않으면 작업이 끝날 때까지 대기한다.
- **invokeAll()**
    - 동시에 실행한 Task 중에 제일 오래걸리는 Task 만큼 시간이 걸린다.
- **invokeAny()**
    - Blocking Call , 동시에 실행한 작업 중 제일 짧게 걸리는 만큼 시간이 걸린다.

<br/>

**CompletableFuture**

- **Asynchronous Programming을 가능하게끔 지원하는 인터페이스**
- **Future 로 처리하기 어려웠던 작업들을 보완하기 위해 만들어졌다.**
    - Future 를 외부에서 완료 시킬 수 없는 것 (취소, Timeout 가능)
    - Blocking Call 을 사용하지 않는 한 Task 종료 시 콜백을 실행할 수 없다.
    - 여러 Future 를 조합할 수 없다
    - 예외 처리용 API 를 제공하지 않는다.

**비동기로 작업 실행하기**

- 리턴 값이 없는 경우 : runAsync()
- 리턴 값이 있는 경우 : supplyAsync()
- (Thread pool) 원하는 Executor 를 사용해서 실행할 수 있다.
    - Default Thread pool : ForkJoinPool.commonPool()

**CallBack Method 제공**

- 리턴 값을 받아 다른 값으로 바꾸는 콜백 thenApply(Function)
- 리턴 값을 또 다른 작업으로 처리하는 콜백 thenAccept(Consumer)
- 리턴 값을 받지 않고 다른 작업을 처리하는 콜백 thenRun(Runnable)

**Method 조합 하기**

- 두 작업이 서로 이어서 실행하도록 조합 thenCompose()
- 두 작업을 독립적으로 실행하고 둘다 종료되면 콜백 실행 thenCombine()
- 여러 작업을 모두 실행하고 모든 작업 결과에 콜백 실행 allOf()
- 여러 작업 중에 가장 빨리 끝난 하나의 결과에 콜백 실행 anyOf()

**예외처리 Method**

- exeptionally(Function)
- handle(BiFunction)