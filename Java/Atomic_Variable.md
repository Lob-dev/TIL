2020.11.04

### Atomic Variable 이란?

원자성을 보장하는 변수이다. 어떠한 작업을 각각의 코어가 처리할 때 데이터를 코어에 할당된 캐시 메모리에 저장하고 사용하게 되는데,

이것을 방지하고 메인 메모리에서만 변수를 조작(읽기, 쓰기) 할 수 있게끔 제약하는 것이다. 이를 통해 하나의 메서드만 접근하게끔 Lock을 거는 Synchronized 를 사용하지 않고도 

병렬 쓰레드 환경에서 안전하게 만들어 낼 수 있었다.



Atomic... 클래스의 내부 메서드는 Unsafe 클래스에서 제공하는 Native 메서드들을 사용하며 **Compared and Swap Algorithm**를 기반으로 동작한다.

<img src="./../image/cas.png" width="90%"></img>

멀티쓰레드환경, 멀티코어 환경에서 각 CPU는 메인 메모리에서 변수값을 참조하는게 아닌, 각각의 CPU 가 가지고 있는 캐시 메모리에서 메모리를 참조하게 된다. 

이떄, 메인메모리에 저장된 값과 CPU의 캐시 메모리에 저장된 값이 다른 경우가 있는데 이것을 가시성 문제라고 하며,

가시성 문제를 해결하기 위해 사용되는 것이 바로 CAS 알고리즘이다. 현재 쓰레드에 저장된 값(캐시 메모리)과 메인메모리에 저장된 값을 비교하여 일치하는 경우 새로운 값으로 교체하고, 

일치하지 않는다면 실패하고 재시도를 한다. 

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

추가적으로 volatile 변수는 가시성 문제를 해결하기 위하여 사용되는 특별한 키워드이며 이 키워드가 붙어있는 객체는 CPU의 캐시 메모리가 아닌 메인 메모리에서 값을 참조하게 된다.

하지만 여러 쓰레드가 접근하였을 경우 안전하지 않기 때문에, CAS 를 같이 사용하여 (적용) 병렬 쓰레드 환경에 대비한다.
