2021.01.26

**CMS GC는 어떻게 STW 최소화하는가?**

1. 대부분의 GC 작업을 application Thread와 Concurrency하게 수행한다. 

    *Initial, Re-Marking 시에 STW가 발생하지만 여러 스레드를 이용함으로써 빨리 처리하고,  Preclean-Up이나 Sweep, Reset 등은 동시에 실행한다.*

    *1번의 Marking(하나의 큰 일시 중단)을 2번의 Marking으로(두 차례의 중단)으로 대체한 것이다.*

<br/>

2. Minor, Major GC의 경우 Live Object를 Compacting하지 않는다. 

    ***Full GC가 수행되었을 때에는 Compacting을 진행한다.***

<br/>

*즉 중단 시간 최소화를 위해 최대한의 일을 Application Thread가 동작하는 동안 처리하는 것이 목적이다. 해당 GC Module을 사용하는 동안 외부, 내부 단편화 문제가 심각해지거나 Young, Old Object의 경쟁 조건이 발생하게 되는 경우 더 큰 Heap(Young Generation)을 할당하라고 명시한다.*

<br/>

참고 자료

- [https://www.oracle.com/technetwork/tutorials/tutorials-1876574.html](https://www.oracle.com/technetwork/tutorials/tutorials-1876574.html)