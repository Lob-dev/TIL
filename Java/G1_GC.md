2021.01.26

<br/>

### **G1 GC 구조**

---

***개념 구조 (모든 영역을 블럭으로 표기하지 않음)***

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd217a512-18c4-4b22-b117-19c55fd8784d%2FUntitled.png?table=block&id=07178718-8db5-424c-b99f-aeb54f19d1d5&spaceId=3f0ac77b-a821-45cf-b1be-d98cfb99a8c7&width=3040&userId=6c65ec07-595a-45d5-a0cf-68530a8bac2d&cache=v2" witdh="80%"/>

G1 GC는 여러 Background Thread를 이용하여 Heap 크기에 따라 1MB~32MB로 분할된 Region들을 지정된 Pointer를 통해 Scan하고 제일 많은 수집 대상이 존재하는 Region에 대해 이름을 지정한다. ***이름이 지정된 영역은 GC의 대상이 된다.*** 

<br/>

**Young Generation Region (Non-Contiguous Region)**

---

Heap 메모리 공간에서 비연속적으로 존재하는 Young Object의 주거지를 의미한다.

해당 영역의 크기는 Heap 전체 크기의 5~60% 까지를 차지할 수 있다.

<br/>

### **G1 GC 특징**

---

- ***G1 GC는 Scan을 하는 도중에 해당 Region에 대한 Compacting도 수행한다.***
- Region은 **모두 같은 크기의 영역을 할당받으며**, Region에 부여된 역할에 따라 그에 맞는 Object가 존재하게 한다. ***(Default Region Size = Heap Size / 2048 {Region count})***
    - XX : G1HeapRegionSize를 통해 Size를 명시적으로 설정할 수 있다.
- JDK 8부터는 Heap 영역에 존재하는 중복된 문자열에 대해 식별하고 여러 참조에 대해 하나의 문자열을 가리키도록 수정하는 최적화 방식이 포함되었다.

<br/>

### **G1 GC Flow**

---

**Evacuation Pauses (Young Generation GC, Minor GC, Young 공간 부족시 발생)**

Young Generation Region에서 발생하는 GC를 말하며 Live Object가 하나 이상의 Survivor Region으로 이동하는 상황을 의미한다. ***(이때 S-T-W가 발생, Multi-Threading 동작)***

*Ageing을 통해 임계값을 만족하는 Object들은 Old Region으로 이동한다.*

<br/>

**G1 Collection Phases (= Old GC)**

1. **Initial Mark (S-T-W)**

    Evacuation Pauses가 실행되는 시점에서 수행되는 동작이다. Survivor Region을 Mark

    *Old GC가 필요한 경우에 동시 수행된다.*

2. **Concurrent Marking (Multi-Threading, Concurrent)**

    Heap 내의 모든 Live Object와 빈 Region 들을 Mark하고 Region의 Live Object 비율 계산

    *해당 동작은 Evacuation Pauses와 같이 수행될 수 있으며 Application과 같이 동작한다.*

3. **Remark (S-T-W)**

    Heap 내의 Live Object Mark 완료 단계. 빈 Region은 Free Region으로 등록

4. **Copying, Cleanup Phase (S-T-W)**

    앞서 2단계에서 계산했던 비율을 가지고 빨리 청소할 수 있는 Region들을 선택하여 Cleanup, Copying을 진행한다. 

    *not-live Object 제거, 빈 Region은 Free Region으로 등록, Survivor → Old Copy 등* 

5. **After Copying, Cleanup Phase (S-T-W)**

    선택된 Region들의 Phase가 종료된 시점, 남은 Region 들에 대해 Cleanup, Copying 진행 

<br/>

### **G1 GC의 장점**

---

- 별도의 STW 없이도 여유 메모리 공간을 압축하는 기능을 제공한다.

    *Compecting 으로 인한 STW 발생 시간을 제거*

- Heap 크기가 클수록 잘 동작하게 된다.
- 특별한 최적화 기능을 제공한다. **(String Deduplication Optimize)**

    *해당 기능은 Evacuation Pauses 중에 발생하며, String의 Hashcode와 값을 비교한다.*

    *최소 수명 즉 AgeThreshold 의 값이 3이상인 String Object를 정리한다.*

- 많은 튜닝 가능성이 존재한다. *(Size, Count, GC 최대 시간, 동작 Thread 수와 실행 빈도 등)*

<br/>

### **G1 GC의 유의점, 단점**

---

- **공간 부족 상태를 조심하여야 한다.**

    이 때 Full GC가 수행되게 되는데, 이 GC는 Single Thread로 동작한다.

    *이를 해결하기 위한 간단한 방법으론 Old Generation Region를 크게 만드는 방법이 있다하지만 이 경우에는 Young Generation Region 영역이 줄어드는 단점이 발생한다.*

- 해당 GC 방식은 6GB 이상의 Heap Management를 목적으로 개발된 모듈이다. 즉 작은 Heap 공간을 가지는 Application에서는 성능을 제대로 발휘하지 못하고 빈번한 Full GC가 발생할 수 있다.

<br/>

### 참고 자료

---

- [https://medium.com/@fnote/g1gc-tuning-ec78d40861d](https://medium.com/@fnote/g1gc-tuning-ec78d40861d)
- [http://java-performance.info/java-string-deduplication/](http://java-performance.info/java-string-deduplication/)
- [https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/G1GettingStarted/index.html](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/G1GettingStarted/index.html)