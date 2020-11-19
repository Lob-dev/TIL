2020.11.04

### Atomic Variable 이란?

원자성을 보장하는 변수이다. 어떠한 작업을 각각의 코어가 처리할 때 데이터를 코어에 할당된 캐시 메모리에 저장하고 사용하게 되는데,

이것을 방지하고 메인 메모리에서만 변수를 조작(읽기, 쓰기) 할 수 있게끔 제약하는 것이다. 이를 통해 하나의 메서드만 접근하게끔 Lock을 거는 Synchronized 를 사용하지 않고도 

병렬 쓰레드 환경에서 안전하게 만들어 낼 수 있었다.



Atomic... 클래스의 내부 메서드는 Unsafe 클래스에서 제공하는 Native 메서드들을 사용하며 **Compared and Swap Algorithm**를 기반으로 동작한다.



<img src="./../image/cas.png" width="90%"></img>

멀티쓰레드환경, 멀티코어 환경에서 각 CPU는 메인 메모리에서 변수값을 참조하는게 아니고 메인 메모리에서 복사된 값이 각각의 CPU 가 가지고 있는 캐시 메모리에서 저장되어 연산하게 된다. (쓰레드를 통하여)

이떄, 메인메모리에 저장된 값과 쓰레드 간의 (CPU의 캐시 메모리에) 저장된 값이 다른 경우가 있는데 이것을 쓰레드 간의 변수 가시성 문제라고 하며,

가시성 문제를 해결하기 위해 사용되는 것이 바로 CAS 알고리즘이다. 현재 쓰레드에 연산하는 값과 메인메모리에 저장된 값을 비교하여 일치하는 경우 새로운 값으로 교체하고, 

일치하지 않는다면 실패하고 재시도를 한다.

<br/>

**Compared and Swap Algorithm 에 대한 세 가지 매개변수**

- 현재 값을 교체해야 하는 메모리 위치 V
- 제일 최근에 쓰레드를 통해 읽은 값 A
- V 위치에 덮어써야 되는 새로운 값 B

**V는 A라는 값을 가지고 있어야되며, 해당 경우 B를 덮어쓰면 된다. (V == A , V = B)**

그렇지 않는 경우에는 값을 반영하지 않고 재시도한다.

<br/>

이를 통해 CPU캐시에 잘못된 값을 참조하여 발생하는 가시성 문제를 방지함으로써 연산이 종료된 후 메인 메모리에 반영되었을 때 참조하게끔 한다. 

```java
public class AtomicInteger extends Number implements java.io.Serializable {

  // 
	private volatile int value; 
        
        public final int incrementAndGet() { 
	    int current; 
            int next;
            do {

	        current = get(); 
                next = current + 1; 

            } while (!compareAndSet(current, next));

            return next; 
        } 
        
        public final boolean compareAndSet(int expect, int update) {
        	return unsafe.compareAndSwapInt(this, valueOffset, expect, update); 
        }	         
}
```
**volatile 키워드는 해당 변수를 메인 메모리에서만 접근 가능하게끔 보장하는 것을 말한다.**

- 읽기 연산, 쓰기 연산등의 결과를 Cache 메모리가 아닌 메인 메모리에서 반영한다.
    - **Cache 메모리에 변수의 값을 복사해가지 않는다.**
    - **volatile 과 연관하여 연산에 대하여 사용되는 모든 변수들도 메인 메모리에서 함께 조작된다.**


**volatile 의 성능 이슈.**

- JVM은 프로그램 성능의 향상을 위해 내부 코드에 대하여서 Code reordering 를 진행하게 되는데, 동기화 되지 않는 코드나 Volatile에 대하여선 진행을 하지 않기에 상대적으로 성능상의 문제점이 있을 수 있다.

**사용할 만한 상황**
- 반대로 하나의 쓰레드만이 쓰기를 하고 다른 쓰레드들이 읽기만 한다면, 사용해도 문제가 없다. -> 최신 값의 제공을 위할 때

**사용하지 못하는 상황**

- 여러 쓰레드가 읽기, 쓰기 연산을 모두 조작하는 경우에는 변경점이 많으므로 문제가 발생할 수 있다. **여러 쓰레드 값을 씀으로써 메인 메모리에 존재하는 변수를 덮어쓸 수 있다.**

이를 방지하기 위해 사용하는 것이 Atomic 패키지이며, 해당 개념은 volatile 변수에 대하여서 원자성을 만족하는 연산을 제공하기 위해 만들어졌다. (CAS 알고리즘)

