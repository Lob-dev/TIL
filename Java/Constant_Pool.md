2020.10.07

<img src="./../image/common%20pool%20for%20String%20Literals.png" width="90%"></img>

모든 리터럴 값 ( 상수 값 ) 이 관리되어 재사용되는 영역으로 기존에는 모두 PERM 영역에   존재하였지만 JDK 8 버전이후 String Pool 은 Heap 영역으로 분리 되었습니다. 