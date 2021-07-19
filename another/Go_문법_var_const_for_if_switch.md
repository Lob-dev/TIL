2021.01.28

<br/>

## **Go 문법 (var, const, for, if, switch)**

<br/>

### **Value**

---

문자열, 정수, 실수, 불 값 등 다양한 타입이 존재한다.

```java
// + 를 통한 문자열 연산 지원
fmt.Println("go" + " lang")

fmt.Println("1+1 =", 1+1) // 2

fmt.Println(true && false)
fmt.Println(true || false)
fmt.Println(!true)
```

<br/>

### **Variable**

---

컴파일러가 변수 타입을 추론하고, 초기화없이 선언된 변수는 0이라는 값을 가지게 된다.

:= 문법은 변수를 선언하는 동시에 초기화하기 위한 단축 표현식이다.

```java
var a string = "initial"
fmt.Println(a)
// a := "initial"

var b, c int = 1, 2
fmt.Println(b, c)
// b, c := 1, 2

d, e := 3, 4
fmt.Println(d, e)

f := "string"
fmt.Println(f)

var i int
fmt.Println(i) // 0 
```

<br/>

### **const**

---

상수 값 선언 방식, const문은 var와 동일하게 사용할 수 있다.

*숫자 값을 가지는 상수는 명시적인 Casting을 하지 않는다면 타입을 가지지 않으며, 변수 할당이나 함수 호출을 통해서도 타입을 부여할 수 있다.*

```java
const s = "string"

const n = 1234

const d = 3000 / n

fmt.Println(d)
fmt.Println(int64(d))
fmt.Println(math.Sin(n))
```

<br/>

### **for**

---

```java
// 초기화 조건 연산 Loop
for i := 1; i <= 10; i++ {

}

// 하나의 조건을 제공한 Loop
i := 1
for i <= 10 {

}

// 무한 Loop
for {
	//... break or Func return이 될 때까지 반복
} 

// 조건식에 따른 Continue도 가능하다.
```

<br/>

### **if , if-else, if-else if-else**

---

```java
if a % b == 0 {

}

if a % b == 0 {

} else {

}

if a :- 9; a < 0 {

} else if a < 10 {

} else {

} 

// go에는 삼항 연산자가 존재하지 않는다.
```

<br/>

### **Switch**

---

```java
switch i {
	case 1:
		fmt.Println("one")
	case 2:
		fmt.Println("two")
	case 3:
		fmt.Println("three")
}

// 동일한 case문에서 여러개의 표현식을 구분하기 위해 콤마(,)를 사용할 수 있다.
switch time.Now().Weekday() {
	case time.Saturday, time.Sunday:
		fmt.Println("It's the weekend")
	default:
		fmt.Println("It's a weekday")
}

// type switch는 값 대신 타입을 비교할 수 있다.
t := time.Now()
switch {
	case t.Hour() < 12:
		fmt.Println("It's before noon")
	default:
		fmt.Println("It's after noon")
}
```