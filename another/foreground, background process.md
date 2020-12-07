2020.12.07


**foreground process**

프로그램 측면에선 현재 유저와 상호작용을 하고있는 프로세스를 말한다. 

작업 제어 측면에선 쉘에서 사용자의 명령어를 해석하고 결과를 반환하기 전까지 다른 작업을 실행할 수 없는 상태를 **foreground** 방식 혹은 **foreground process** 라고 한다.

- 프로그램 측면 : 현재 사용하고 있는 웹 브라우저, 게임 등
- 작업 제어 측면 : 쉘과 같은 text command interpreter

<br/>

**background process**

사용자 입력과 상관없이 실행되는 독자적인 프로세스를 말한다. 또는 foreground process 가    실행되는 동안 보이지 않는 영역에서 다른 프로세스가 실행되고 있음도 의미한다.

- 백신, 서버 데몬 등
- 쉘 명령에서 suffix로 & 를 사용하는 명령, 프로세스 등