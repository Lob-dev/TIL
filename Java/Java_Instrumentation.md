2021.01.26

### **Java Instrumentation**

<br/>

기존에 Compile된 Java Class 파일에 Byte Code를 추가하는 기능을 제공하는 API이다.

응용 프로그램 상태 또는 동작을 수정하지 않고, 단순히 코드를 추가하는 도구들의 집합을 의미하기도 한다. ***Java Agent, 프로파일러, 커버리지 분석기, 이벤트 로거 등***

<br/>

**관련 Library**

- **Lombok(Agent api)**
- **CGLIB (Bytecode Generator)**

<br/>

### **Java Instrumentation API를 사용하는 방법**

---

- JVM 시작시 -javaagent 매개 변수를 사용하여 Agent Instance를 **Static Loading**
- Java Attach API를 이용하여 JVM에 **Dynamic Loading**

<br/>

**Static Loading**

---

Application 시작시 Agent를 Loading 하는 것을 의미하며, 코드가 실행되기전에 Bytecode를 수정한다.

<br/>

**Static Loading Flow**

---

1. JVM Starting
2. Agent premain method → Transforming class XxxClass 
3. Application Starting

<br/>

**Dynamic Loading**

---

실행중인 Application에 Agent를 Loading 하는 것을 의미한다. 코드가 실행된 이후에 Bytecode를 수정할 수 있다.

<br/>

**Dynamic Loading Flow**

---

1. Application Starting
2. Attaching to target JVM 
3. Load Java Agent
4. AgentMain method → Transforming class XxxClass