2021.01.26

<br/>

### **Shallow Copy**

---

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F40fedb7d-af3e-4c8f-9b5a-518f5f37364e%2FUntitled.png?table=block&id=39bab5f4-661c-42c5-bbf8-a0ec5520e21c&spaceId=3f0ac77b-a821-45cf-b1be-d98cfb99a8c7&width=3040&userId=6c65ec07-595a-45d5-a0cf-68530a8bac2d&cache=v2" witdh="80%" />

1개 이상의 지역, 인스턴스 변수 등이 같은 객체를 참조함으로써 동일한 상태 변화를 겪는 것, 한 쪽에서 가한 영향이 다른 쪽에게도 미치게 된다. (동일한 객체를 참조하는 것)

*주소 값을 복사하여 참조하기에 원본과 사본 객체가 동기화된 상태이다.*

<br/>

### **Deep Copy**

---

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2eef9bff-221d-445e-b4a3-a3847fb14828%2FUntitled.png?table=block&id=68172dec-b860-48f9-a60b-04d1fc55e551&spaceId=3f0ac77b-a821-45cf-b1be-d98cfb99a8c7&width=3040&userId=6c65ec07-595a-45d5-a0cf-68530a8bac2d&cache=v2" witdh="80%" />

같은 객체를 참조하는 것이 아닌 같은 값만을 가지고 별도의 Hashcode(식별 값)을 가지는 객체를 참조하는 것을 의미한다. (동등한 객체를 참조하는 것) 

*해당 객체의 값을 복사하여 새로운 객체로 만드는 것이다.*