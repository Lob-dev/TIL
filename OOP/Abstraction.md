2020.09.30

**추상화 (Abstraction)**
+ 데이터나 프로세스 등을 의미가 비슷한 개념이나 의미있는 표현으로 정의하는 과정 혹은
+ 어떠한 사물 집합에 대해 공통적인 상태와 메서드를 추출함으로써 상위 분류를 만드는 과정
  
  + 특정한 성질 : 사용자에서 ID, Email, Password 를 뽑아내 User Class 를 만드는 것.
  + 공통된 성질 (일반화) : GTX 1660, RTX 2070, RX 580 = 그래픽 카드 (일반화)
  + **공통된 성질을 뽑아내는 추상화는 다형성과 관련된다.** 여러가지 구현 클래스가 있는 경우 해당 클래스들을 대표하는 상위 타입을 도출한다. 흔히 인터페이스로 많이 표현한다.