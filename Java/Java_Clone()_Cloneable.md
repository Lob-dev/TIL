2021.02.02

<br/>

### Clone()?

---

```java
// Java Shallow Copy
// Java의 대입 연산자는 기본적으로 같은 인스턴스 참조 값을 공유하게 된다.
Some a = new Some("value");
Some b = a;

// Java Deep Copy
// Java의 Deep Copy는 Clone() method를 통해 새로운 동등한 인스턴스를 반환한다.
Some a = new Some("value");
Some b = a.clone();

```

<br/>

### Cloneable Interface

---

해당 클래스가 Clone()을 사용할 수 있는 것임을 알려주는 역할을 하는 인터페이스이다. 

*이러한 인터페이스를 마커 인터페이스라고 한다. 즉 내부에 메서드, 상수가 존재하지 않고, 객체에 대한 추가적인 정보를 Runtime 시점에 제공하는 역할을 하는 인터페이스이다.*

*Serializable, Cloneable, Remote ...* 

<br/>

*해당 인터페이스를 구현하지 않았다면 JVM은 CloneNotSupportedException을 발생시킨다.*

[https://www.baeldung.com/java-marker-interfaces#:~:text=A marker interface is an,also called a tagging interface](https://www.baeldung.com/java-marker-interfaces#:~:text=A%20marker%20interface%20is%20an,also%20called%20a%20tagging%20interface).

<br/>

Custom *Marker Interface Example*

```java
public interface Deletable {
}

public class XxxEntity implements Deletable {

}

public class XxxRepository {

		public boolean delete(Entity entity) {
				if (!(entity instaceof Deletable)) {
						return false;
				}

				// Delete SomeThings....
		}
}
```