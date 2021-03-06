2020.09.30

**OOP**
  + 객체의 관점에서 객체간 유기적인 결합을 통해 기능이 실행되게끔 개발하는 것이다.
    + OOP를 통하여서 객체가 행위에 대해 책임을 질 수 있고, 자신 만의 상태 값을 통해서 독립적으로 행위할 수 있다.
    + 객체 내부에 행위가 존재함으로써 좀 더 직관적이며, 변경의 여파가 적음으로 유지보수하기 좋다.
  
<br/>

+ 어떻게 생겨난 것일까?
  + OOP 이전의 프로그래밍 방식은 **절차지향 방식**이다. 절차지향 방식은 프로그램을 명령의 집합으로 보고 순서에 맞게 **기능을 처리**하는 것을 중요시하였다. (반복, 제어...)

  + 간단한 로직(알고리즘)에서는 문제가 없었지만 **규모가 커질수록**, 처리하는 **기능이 많을수록**,**코드의 복잡도가 증가**하여 주로 말하는 **스파게티 코드**가 되어버리는 경우도 흔했다.

  + 해당 문제를 해결하기 위하여 **구조적 프로그래밍 방식**과 **함수(Method)라는 것이 등장**하였고, 그것을 이용해 **큰 문제를 작은 문제들로 쪼개어 작성**하여 **복잡도 문제를 해결**하였다.    **(하향식, 분할정복)**

  + 위의 두 방식은 결국 **프로그램을 논리, 명령어의 집합으로만 보는 방식**이다.

  + 객체지향 방식은 절차지향의 요소와 구조적 프로그래밍의 요소를 가지고, 현실세계의 사물을 **분류, 추상화하고 각각 책임(행위)을 부여한 뒤** 해당 객체들을 **조립하여 문제를 해결하는** **방식이다. (상향식)** 