2020.10.25

<br/>

### 클래스 맴버 변수 와 인스턴스 맴버 변수의 생명주기.

<br/>

클래스 맴버

여기서 말하는 Class 는 Metaspace 영역에 저장되는 Class 를 뜻하며, Class 맴버는 인스턴스화 되지 않는 맴버 변수를 말합니다. 즉 Static Variable 입니다.

Static Variable은 Heap 영역에서 관리가 됩니다. (JDK 8+ )

Static Variable은 Class 정보와 함께 initialized 됩니다.

```java
public class StaticVariable {
		
    public static String staticVariable = "static value";
		
    public static void printStaticVariable () {
        System.out.println(staticVariable);
    }
		
    public static void main(String[] args) {
        StaticVariable.printStaticVariable(); 
        String localVariable = StaticVariable.staticVariable;
        String localVariable2 = "static value";

        System.out.println("localVariable.toString() = " + localVariable.toString());
        System.out.println("localVariable.hashCode() = " + localVariable.hashCode());
        System.out.println("localVariable.hashCode() = " + StaticVariable.staticVariable.hashCode());
        System.out.println(localVariable == localVariable2);
        System.out.println(StaticVariable.staticVariable == localVariable2);
        System.out.println("localVariable2.hashCode() = " + localVariable2.hashCode());
    }
}
```

```java
static value
localVariable.toString() = static value
localVariable.hashCode() = -118462145
localVariable.hashCode() = -118462145
true
true
true
localVariable2.hashCode() = -118462145
```

<br/>

인스턴스 맴버

Instance 맴버는 말 그대로 Instance 화 되어야 만들어지고 접근할 수 있는 맴버를 말하며, 클래스     내부와 메서드 외부에 존재하는 Stiatic이 아닌 변수들을 말합니다. 

객체가 인스턴스화 되었을 때 (참조 변수를 통해 new 되었을 때 Heap 영역에 생성됩니다.)

해당 변수는 getter setter 등의 메서드로 접근할 수 있습니다. (캡슐화 되어있기 때문에)