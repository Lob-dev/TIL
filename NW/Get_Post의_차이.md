2021.01.27

<br/>

### **Get, Post의 차이?**

<br/>

**GET Method**

Request Body가 존재하지 않으며, 멱등성을 지키는 HTTP Method이다. 해당 요청에 대한 모든 정보는 Query String 형태로 넘어가게 된다. *모든 매개 변수 정보는 Client에 남게 된다.*

요청에 대한 캐싱이 가능하고 기본적으로 문자열 유형만을 지원합니다. 

<br/>

**POST Method**

Request Body가 존재하며, 멱등성을 지키지 못하는 HTTP Method이다. 해당 요청에 대한 모든 정보는 Request Body, Query String 형태로 넘길 수 있으며, 모든 매개 변수 정보는 Client에 남지 않는다.

요청에 대한 캐싱이 불가능하고 문자열, 숫자, 이진 데이터등 여러 유형을 지원합니다.