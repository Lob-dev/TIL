2020.10.29

<br/>

아스키, 유키코드, 인코딩, 디코딩 (한글 관련) 

컴퓨터는 문자를 인식할 수 없기에 모든 것이 숫자로 변환되어 저장된다,  사람이 인식할 수 있게끔 문자로 변환하기 위해서는 기준이 있어야 했는데 문자와 숫자 순서를 정해 지정한 다음 집합화하여 만든 것이 **ASCII 나 Unicode 이다**.

즉 아스키, 유니코드는 문자와 숫자를 매칭시킨 charSet이다.

<br/>

**문자 { 코드 }**

- **"A" → 65 (10진) → 0x41 (16진) → 1000001 (2진수) - ASCII charset**
- **"A" → U+0041 (UNICODE )→ 41 (10진)                  - Unicode charset**

<br/>

**ASCII charset**

미국의 정보 교환을 위해 설계된 것으로 **영문 알파벳을 사용하는 대표적인 문자 표**이다.

**1바이트로 구성되어 있다. [ 1개의 페리티 비트 (데이터 검증), 7개의 표현 비트]**

<br/>

**Unicode charset**

**전 세계의 모든 문자를 컴퓨터에서 일관되게 표현하고 다룰 수 있도록 설계된 표준 코드이다.**

- **UTF-8** Encoding **( Chrome, IE11, Edge, Apache ) 1 byte ~ 4 byte 가변 표현 ASC 호환**
- **UTF-16** Encoding **(JAVA Default Encoding)  2 byte의 고정 표현.                  ASC 호환**
- **EUC-KR** Encoding (**Unix**)
- **CP949** Encoding **(EUC-KR 확장, Windows )**

인코딩은 아스키나 유니코드같은 문자표를 기준으로 문자를 기계어로 변환하는 것을 말하며 반대의 상황은 디코딩이라고 한다.

**(Windows..) EUC-KR**, **CP949** ←→ **(Web..) UTF-8 별도 인코딩 없이 사용할 경우. 깨지는 경우가 많다.**

PC, OS, 브라우저, 시스템 등에 따라서 사용되는 표준 문자 코드가 다르며, 이것들을 정상적으로      교환, 전송하기 위해서는 두 시스템이 같은, 혹은 호환되는 문자 코드를 사용해야 한다.

위에서 이야기한 인코딩과 디코딩을 통해서 각 시스템 간의 정보를 변환하여 처리할 수 있게 되었다.