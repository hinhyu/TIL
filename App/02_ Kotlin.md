#### 코틀린은 intelliJ IDEA라는 제품으로 유명한 JetBrains가 2011년 7월 공개한 언어.
https://kotlinlang.org/
자바와 100% 호환되어 자바 코드를 모두 코틀린으로 대체할 수 있음

#### 구글이 2017년 안드로이드 공식 언어로 코틀린을 추가
2010년 이후 8년간 오라클과 구글의 자바 라이센스 소송에서 오라클이 승리

#### 특징
1. Java와 100% 상호 호환되므로 Java코드를 완전히 대체
2. Java보다 문법이 간결
3. 프로그램의 안정성을 높여줌 (원천적으로 Null의 환경을 막음, Null Point Exception 막음)
4. var또는 val예약어를 통해 데이터 형식을 선언하지 않고 변수를 선언할 수 있음 (int가 없음)


변수와 데이터 형식

변수 타입이 변수 이름 뒤에 붙는 형태
정수형 변수, 실수형 변수, 문자형 변수, 문자열 변수를 선언하고 값을 대입한 후 출력하는 예제

```kotlin
fun main() {
	var var1 : Irt = 10
	var var2 : Float = 10.1f
	var var3 : Double = 10.2
	var var4 : Char = '안'
	var var5 : String = "안드로이드"

	plintln(var1)
	plintln(var2)
	plintln(var3)
	plintln(var4)
	plintln(var5)

}
```

Kotlin : 데이터 타입이 대문자로 시작

### Kotlin의 변수 선언 방식
#### 암시적 선언
변수의 데이터 형식을 지정하지 않고, 대입되는 값에 따라 자동으로 변수의 데이터 형식이 지정
**단, 초기화하지 않는 경우에는 데이터 형식을 반드시 명시해야 한다.**

```kotlin
var var1 = 10
var var2 = 10.1f
var var3 = 10.2
var var4 = '안'
var var5 = "안드로이드"
```

뒤에오는 초기값 때문에 생략이 가능하다.
뒤에있는 10이라는 초기값이 var1에 대해서 데이터타입이 선언되어 있지 않더라도 Int라는 데이터타입으로 인식하고 byte크기를 설정한다. 

#### var(variable)
일반 변수를 선언할 때 사용
필요할 때마다 계속 다른 값을 대입 가능

#### val(value)
변수 선언과 동시에 값을 대입하거나, 초기화 없이 선언한 후에 한 번만 값을 대입 가능
한 번 값을 대입하고 나면 **값을 변경할 수 없음**

```kotlin
var myVar : Int = 100
myVar = 200 //정상

val myVal : Int = 100
myVal = 200 //오류
```

### 변수와 데이터 형식

#### 데이터 형식 변환

캐스팅 연산자 사용
Kotlin에서 제공하는 toInt()나 toDouble()등의 정적 메소드 사용

```kotlin
var a : Int = "100".toInt() // ""은 String인데 뒤에 toInt()함수를 이용해 Int로 변환
var b : Double = "100.123".toDouble()
```

해당되는 데이터 자체에 함수를 붙여 변환이 가능하다.

#### null 사용

Kotlin은 기본적으로 변수에 null값을 넣지 못함
- 변수를 선언할 때 데이터 형식 뒤에 ?를 붙여야 null대입 가능

```kotlin
var notNull : Int = null //오류
var okNull : Int? = null //정상
```

#### 변수가 null값이 아니라고 표시해야 하는 경우
!!로 나타남
- 이 경우 null값이 들어가면 오류 발생

```kotlin
var ary = ArrayLinst<Int>(1) //1개짜리 배열 리스트
ary!!.add(100) //값 100을 추가
```

ary 배열 변수는 null이 될 수 없다.

