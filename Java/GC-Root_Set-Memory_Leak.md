2020.10.20

### GC Root set의 조건

- **Stack 영역의 데이터** (**Local Variable, Operand Stack** 에 존재하는 객체 **참조 값** 등)
- **heap 영역의 String Pool 데이터 (Static Object) , Meta 영역의 Constant Pool 참조**
- **JNI 를 통해 생성된 객체들 (Java Native Interface)**
- **load 된 Class 의 Meta Data**
- **Heap 영역 내부에서 다른 객체를 참조 중인 객체 (앞선 객체가 연결된 경우)**

어떠한 객체의 **유효한 참조가 존재**한다면 **Reachable Object**라고 하고 그렇지 않다면, **UnReachable Object**라고 한다.

조건의 예 :

- Stack 에서 Heap 영역의 값을 참조하는 경우 (참조 변수, 객체)
- Heap 영역에 존재하는 객체나 Stack 영역의 변수가 Constant Pool 을 참조, 간접 참조    하는 경우 등

개발자는 객체의 Reachability를 조절하기 위해서 

- SoftReference
- WeakReference
- PhantomReference
- ReferenceQueue

등을 사용할 수 있으며, 내부 캐시등을 구현하는데에는 WeakReference 혹은 이를 이용한 WeakHashMap만으로도 충분하다고 한다.

<br/>

### Memory Leak

Memory Leak은 참조는 되어 있으나, 사용되지 않고 있는 객체인 Reachable But Not Live 들이 제거되지 않고 계속 남아있는 상태로 누적될 경우 발생한다. ( OOM )  
<br/>
**Static Collection, Remove 되지 않고 참조 값을 통해 간접 참조 중인 객체들 등**

<br/>

**Java 에서 Memory Leak 내는 방법 (~ JDK 7)**

1. **GC는 Heap 영역**에서만 이루어지게 된다.
2. **Static 이 붙은 Class, Method, String, Collection 등은 PERM 영역**에 저장된다.
3. **Static 이 붙은 Collection 객체를 선언**하고 **데이터를 쌓게되면.**
4. **GC에서 도달할 수 없는 ( 관리할 수 없는 ) 메모리 사용량이 증가**한다.
5. **4번 현상이 계속 발생**하면, **GC가 실행되지 않으면서 OutOfMemoryError를 발생**시킨다.
6. **결국 시스템을 재 시작하지 않으면 해당 인스턴스는 서비스할 수 없게된다.**
- **메모리 릭의 원인은 HeapDump를 통해 확인이 가능하다. (MAT 같은 분석툴을 이용)**

<br/>

**~ JDK 8**

1. Collection 참조 변수를 만들고 해당 객체의 내부에 값을 저장한다.
2. 해당 값을 꺼내어서 지역변수에서 참조하고 사용한다.
3. 지역 변수에만 Null 처리를 진행한다. → Collection 내부에는 존재한다.
4. Collection 객체를 정리하지 않고 1, 2 의 행위를 반복하면 Not Live 들이 누적된다.