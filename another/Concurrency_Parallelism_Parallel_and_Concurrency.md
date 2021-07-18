2021.01.23

## **Concurrency**

---

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fde7441f9-17e9-4465-9297-1c8f95fdd5b3%2FUntitled.png?table=block&id=36d33adc-1976-4611-8f08-b69b062b6ff5&spaceId=3f0ac77b-a821-45cf-b1be-d98cfb99a8c7&width=2960&userId=6c65ec07-595a-45d5-a0cf-68530a8bac2d&cache=v2" width=90%></img>

하나의 코어가 여러 프로세스를 번갈아가며 실행하는 것을 의미한다. 이는 사용자에게 동시에 실행되는 것처럼 보이게 만드는 효과를 가지며, 단위 시간내에 더 많은 일을 처리한다.

<br/>

*프로세스 간에는 컨텍스트 스위칭이 발생한다.* 

<br/>

### **Concurrency의 장단점**

---

**장점**

- CPU의 처리량이 증가한다.
- 자원의 활용도가 증가한다.
- 프로세스간의 대기시간이 감소된다.

<br/>

**단점**

- Context Switching에 대한 Overhead가 발생한다.

<br/>
<br/>

## **Parallelism**

---

**하나의 프로세스를 분할하여 처리**

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F98870fb9-fb91-4986-83c8-e9d414983406%2FUntitled.png?table=block&id=7a95e116-4bd7-451e-93fe-22eb2e9d7fd9&spaceId=3f0ac77b-a821-45cf-b1be-d98cfb99a8c7&width=2960&userId=6c65ec07-595a-45d5-a0cf-68530a8bac2d&cache=v2" width=90%></img>

여러개의 코어가 하나의 프로세스의 작업을 분할하여 처리하는 것을 의미한다. 이는 내부적으로 동작하는 스레드의 개수만큼 CPU에 할당할 수 있음을 의미한다.

<br/>

*화면을 랜더링하는 스레드, 계산을 진행하는 스레드, 서버와 통신하는 스레드 등...*

<br/>

**한번에 여러 프로세스를 실행**

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc2c51aad-067d-47df-bfa8-de87e1d0487a%2FUntitled.png?table=block&id=87ae378f-aab3-44a2-9c33-eeab81fb057f&spaceId=3f0ac77b-a821-45cf-b1be-d98cfb99a8c7&width=2960&userId=6c65ec07-595a-45d5-a0cf-68530a8bac2d&cache=v2" width=90%></img>

이와 같이 각 코어가 별개의 프로세스를 동작함으로써 단위 시간내에 여러 프로세서를 동작시킬 수도 있다.

<br/>
<br/>

### **Parallelism의 장단점**

---

**장점**

- 하나의 프로세스를 분할하여 여러 작업으로 처리할 수 있다.
- 하나의 작업에 대해 가용 자원을 더 많이 할당할 수 있다.
- 여러 프로세스에 대해서도 동시에 수행할 수 있다.

<br/>

**단점**

- 단일 코어 방식보다 어려운 작성 방식을 가진다.
- 프로세스 분할 처리시 발생하는 추가 비용이 더 크다. *데이터 전송, 동기화, 통신, 전환 등*
- 각각의 시스템 아키텍처에 맞게 알고리즘 로직의 조정이 필요하다.

<br/>
<br/>

## **Parallel and Concurrency**

---

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fda09e350-f5c2-40ac-ad00-1a3cb534256d%2FUntitled.png?table=block&id=ea34c9e2-1ea5-4b7d-931a-6ceee9cbf11d&spaceId=3f0ac77b-a821-45cf-b1be-d98cfb99a8c7&width=2960&userId=6c65ec07-595a-45d5-a0cf-68530a8bac2d&cache=v2" width=90%></img>

여러개의 코어에서 여러 프로세스들을 번갈아 실행하는 상황을 의미한다.

<br/>

*물리적인 개념(Parallel)과 논리적인 개념(Context Switching)이 연계된 것이다.*

<br/>

**참고 자료**

- [https://openclassrooms.com/en/courses/5684021-scale-up-your-code-with-java-concurrency/5684028-identify-the-advantages-of-concurrency-and-parallelism](https://openclassrooms.com/en/courses/5684021-scale-up-your-code-with-java-concurrency/5684028-identify-the-advantages-of-concurrency-and-parallelism)
- [http://tutorials.jenkov.com/java-concurrency/concurrency-vs-parallelism.html](http://tutorials.jenkov.com/java-concurrency/concurrency-vs-parallelism.html)
- [https://medium.com/mineiros/how-to-use-multithreading-and-multiprocessing-a-beginners-guide-to-parallel-and-concurrent-a69b9dd21e9d](https://medium.com/mineiros/how-to-use-multithreading-and-multiprocessing-a-beginners-guide-to-parallel-and-concurrent-a69b9dd21e9d)