**2장에서 다루는 내용**

- **함수, 변수, 클래스, enum, 프로퍼티를 선언하는 방법**
- **제어 구조**
- **스마트 캐스트**
- **예외 던지기와 예외 잡기.**

---

### **2.1 기본 요소 : 함수와 변수.**

**2.1.1 Hello world!**

```kotlin
fun main(args : Arrays<String>){
		println("Hello, world!")
}
```

**2.1.2 함수**

```kotlin
fun max(a : Int, b : Int) : Int {
		return if(a>b) a else b
}
println(max(1, 2))

```

```kotlin
fun max(a: Int, b: Int) : Int = if (a > b) a else b
```

```kotlin
fun max(a ; Int, b: Int) = if( a > b ) a else b
```

**2.1.3 변수**

```kotlin
val question = 
	"삶, 우주, 그리고 모든 것에 대한 궁극적인 질문"

val answer = 42

val answer : Int = 42
```

```kotlin
val yearsToCompute = 7.5e6
```

```kotlin
val answer : Int
answer = 42
```

**변경 가능한 변수와 변경 불가능한 변수**

**: 변수 선언 시 사용하는 키워드는 다음과 같은 2가지가 있다.** 

- **val (값을 뜻하는 value에서 따옴 ) - 변경 불가능한 참조를 저장하는 변수다. val로 선언된 변수는 일단 초기화하고 나면 제대임이 불가능하다. 자바로 말하자면 final변수에 해당한다.**
- **var - 변경 가능한 참조다. 이런 변수의 값은 바뀔 수 있다. 자바의 일반 변수에 해당한다.**

val 변수는 블록을 실행할 때 정확히 한 번만 초기화해야 한다. 하지만 어떤 블록이 실행될 때 오직 한 초기화 문장만 실행됨을 컴파일러가 확인할 수 있다면 조건에 따라 val 값을 다른 여러 값으로 초기화할 수도 있다. 

```kotlin
val message : String
if(canPerformOperation()){
		message = "Success"
		// ... 연산을 수행한다.

}else{
		message = "Failed"
}
```

val 참조 자체는 불변일지라도 그 참조가 가리키는 객체의 내부 값은 변경될 수 있다는 사실을 기억하라.

예를 들어 다음 코드도 완전히 올바른 코틀린 코드다.

```kotlin
val languages = arrayListOf("Java")  // 불변 참조를 선언한다. 
languages.add("Kotlin") // 참조가 가리키는 객체 내부를 변경한다. 
```

예를 들어 다음 코드는 컴파일할 수 없다.

```kotlin
var answer = 42
answer = "no answer" // Error : type mismatch" 컴파일 오류 발생. 
```

문자열 리터럴에서 컴파일 오류가 발생한다. 이유는 그 타입이 컴파일러가 기대하는 타입과 다르기 때문이다. 

컴파일러는 변수 선언 시점의 초기화 식으로부터 변수의 타입을 추론하며, 변수 선언 이후 변수 재대입이 이뤄질 때는 이미 추론한 변수의 타입을 염두에 두고 대입문의 타입을 검사한다. 

**2.1.4 더 쉽게 문자열 형식 지정 : 문자열 템플릿.** 

```kotlin
fun main(args : Array<String>){
		val name : if (args.size > 0) args[0] else "Kotlin"
		println("Hello, $name!")

}
```

복합한 식도 중괄호({})로 둘러싸서 문자열 템플릿 안에 넣을 수 있다. 

```kotlin
fun main(args : Array<String>){
		if(args.size > 0){
				println("Hello, ${args[0]}!") // args 배열의 첫 번째 원소를 넣기 위해 ${} 구문을 사용한다. 
		}
}
```

중괄호로 둘러싼 식 안에서 큰 따옴표를 사용할 수도 있다. 

```kotlin
fun main(args : Array<String>){
		println("Hello, ${if (args.size > 0) args[0] else "someone"}!")
}
```

### 2.2 클래스와 프로퍼티

간단한 자바 클래스

```kotlin
public class Person {
		private final String name;

		public Person(String name){
				this.name = name;
		}
		public String getName(){
				return name; 
		}
}
```

코틀린으로 변환한 Person 클래스

```kotlin
class Person(val name : String)
```

이런 유형의 클래스(코드가 없이 데이터만 저장하는 클래스)를 값 객체라 부르며, 다양한 언어가 객 객체를 간결하게 기술할 수 있는 구문을 제공한다. 

자바를 코틀린으로 변환한 결과, public  가시성 변경자가 사라졌음을 확인하라. 

코틀린의 기본 가시성은 public 이므로 이런 경우 변경자를 생략해도 된다. 

**2.2.1 프로퍼티**

> 여러분도 분명히 알고 있겠지만 클래스라는 개념의 목적은 데이터를 캡슐화 하고 캡슐화한 데이터를 다루는 코드를 한 주체 아래 가두는 것이다.
> 

- 자바에서는 데이터를 필드(field)에 저장하며, 멤버 필드의 가시성은 보통 비공개(private)다.
- 클래스는 자신을 사용하는 클라이언트가 그 데이터에 접근하는 통로로 쓸 수 있는 접근자 메서드를 제공한다.
- 보통은 필드를 읽기 위한 게터(getter)를 제공하고 필드를 변경하게 허용해야 할 경우 세터(setteR)를 추가 제공할 수 있다.

자바에서는 필드와 접근자를 한데 묶어 프로퍼티라고 부르며, 프로퍼티라는 개념을 활용하는 프레임워크가 많다. 

- 코틀린은 프로퍼티를 언어 기본 기능으로 제공하며, 코틀린 프로퍼티는 자바의 필드와 접근자 메서드를 완전히 대신한다.
- 클래스에서 프로퍼티를 선언할 때는 앞에서 살펴본 변수를 선언하는 방법과 마찬가지로 val이나 var 를 사용한다.
- val로 선언한 프로퍼티는 읽기 전용이며, var로 선언한 프로퍼티는 변경 가능하다.

ex) 클래스 안에 서 변경 가능한 프로퍼티 선언하기

```kotlin
class Peron { 
		val name : String,   // 읽기 전용 프로퍼티로, 코틀린은 (비공개) 필드와 필드를 읽는 단순한 (공개) 게터를 만들어 낸다. 
		var isMarried : Boolean // 쓸 수 있는 프로퍼티로, 코틀린은 (비공개) 필드, (공개) 게터, (공개) 세터를 만들어낸다. 
}
```

ex) 자바에서 Person 클래스를 사용하는 방법

```kotlin
Person person = new Person("Bob", true);
System.out.println(person.getName()); // Bob
System.out.println(person.isMarried()); // true
```

ex) 코틀린에서 Person 클래스 사용하기.

```kotlin
val person = Person("Bob", true)
println(person.name) // Bob
println(person.isMarried) // true
```

자바에서는 `**person.setMarried(false)**` 로 어떤 사람이 이혼했다는 사실을 기록하지만, 코틀린에서는 `**person.isMarried = false**` 를 쓴다.

**2.2.2 커스텀 접근자.** 

: 프로퍼티의 접근자를 직접 작성하는 방법. 

```kotlin
class Rectangle(val height : Int, val : width : Int){
		val isSquare : Boolean
				get(){
					return height == width
				}
}
```

```kotlin
val rectangle = Rectangle(41, 43)
println(rectangle.isSquare)
// false
```

**2.2.3 코틀린 소스코드 구조 : 디렉터리와 패키지**

: 자바의 경우 모든 클래스를 패키지 단위로 관리한다는 사실을 잘 알고 있을 것이다. 코틀린에도 자바와 비슷한 개념의 패키지가 있다.  모든 코틀린 파일의 맨 앞에 package 문을 넣을 수 있다. 

Ex) 클래스와 함수 선언을 패키지에 넣기. 

```kotlin
package geometry.shapes

import java.util.Random

class Rectangle (val height : Int, val width: Int){
		val isSquare : Boolean
				get() = height == width
}

fun createRandomRectangle() : Rectangle {
		val random = Random()
		return Rectangle(random.nextInt(), random.nextInt())

}
```

Ex) 다른 패키지에 있는 함수 임포트하기

```kotlin
package geometry.example

import geometry.shapes.createRandomRectangle // 이름으로 함수 임포트하기

fun main(args : Array<String>){
		println(createRandomRectangle().isSquare) // "true"가 아주 드물게 출력된다. 
}
```

### 2.3 선택 표현과 처리 : enum 과 when

: when은 자바의 switch를 대치하되 훨씬 더 강력하며, 앞으로 더 자주 사용할 프로그래밍 요소라고 생각할 수 있다. 

**2.3.1 enum 클래스 정의**

Ex) 간단한 enum 클래스 정의하기

```kotlin
enum class Color { 
		RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}
```

코틀린에서는 enum을 소프트 키워드라고 부른다. 

*enum은 class 앞에 있을 떄는 특별한 의미를 지니지만 다른 곳에서는 이름에 사용할 수 있다. 

**Ex) 프로퍼티와 메서드가 있는 enum 클래스 선언하기.**

```kotlin
enum class Color ( 
		val r: Int, val : g : Int, val b: Int // 상주 프로퍼티를 정의한다.
) {
		RED(255, 0, 0), ORANGE(255, 165, 0), // 각 상수를 생성할 때 그에 대한 프로퍼티 값을 지정한다. 
		YELLOW(255, 255, 0), GREEN(0, 255, 0), BLUE(0, 0, 255), 
		INDIGO(75, 0, 130), VIOLET(238, 130, 238); // 여기 반드시 세미콜론을 사용해야 한다. 

		fun rgb() = (r * 256 + g) * 256 + b // enum 클래스 안에서 메서드를 정의한다. 

}

println(Color.BLUE.rgb())
255
```

enum에서도 일반적인 클래스와 마찬가지로 생성자와 프로퍼티를 선언한다. 각 enum 상수를 정의할 때는 그 상수에 해당하는 프로퍼티 값을 지정해야만 한다. 이 예제에서는 코틀린에서 유일하게 세미콜론(;)이 필수인 부분을 볼 수 있다. 

2.3.2 when으로 enum 클래스 다루기

Ex) when을 사용해 올바른 enum 값 찾기.

```kotlin
fun getMnemonic(color : Color) = 
		when ( color ){
				Color.READ -> "Richard"
				Color.ORANGE -> "Of"
				Color.YELLOW -> "York"
				Color.GREEN -> "Gave"
				Color.BLUE -> "Battle"
				Color.INDIGO -> "In"
				Color.VIOLET -> "Vain"
		}

println(getMnemonic(Color.BLUE))
// Battle
```

Ex) 한 when 분기 안에 여러 값 사용하기.

```kotlin
fun getWarmth(color : Color) = when(color) {
			Color.RED, Color.ORANGE, Color.YELLOW -> "warm"
			Color.GREEN -> "neutral"
			Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold"

}

println(getWarmth(Color.ORANGE))
// warm
```

Ex) enum 사우 값을 임포트해서 enum 클래스 수식자 없이 enum 사용하기.

```kotlin
import ch02.colors.Color
import ch02.colors.Color.*

fun getWarmth(color: Color) = when(color){
		RED, ORANGE, YELLOW -> "warn"  // 임포트한 enum 상수를 이름만으로 사용한다. 
		GREEN -> "neutral"
		BLUE, INDIGO, VIOLET -> "cold"
}
```

2.3.3 when과 임의의 객체를 함께 사용.

코틀린에서 when은 자바의 switch 보다 훨씬 더 강력하다. 

Ex) when 의 분기 조건에 여러 다른 객체 사용하기. 

```kotlin
fun mix(c1: Color, c2: Color) = 
		when (setOf(c1, c2)){
				setOf(RED, YELLOW) -> ORANGE
				setOf(YELLOW, BLUE) -> GREEN
				setOf(BLUE, VIOLET) -> INDIGO
				else -> throw Exception("Dirty color")
		}

println(mix(BLUE, YELLOW)) // GREEN
```

2.3.4 인자 없는 when 사용

: 인자가 없는 when을 사용하면 불필요한 객체 생성을 막을 수 있다.

Ex) 인자 없는 when

```kotlin
fun mixOptimized(cl: Color, c2: Color) = 

		when {
				(c1 == RED && c2 == YELLOW) ||
				(c1 == YELLOW && c2 == RED) -> 
						ORANGE
				(c1 == YELLOW && c2 == BLUE) ||
				(c1 == BLUE && c2 == YELLOW) ->
						GREEN
				(c1 == BLUE && c2 == VIOLET) ||
				(c1 == VIOLET && c2 == BLUE->
						INDIGO

				else -> throw Exception("Dirty color")
				
		}

println(mixOptimized(BLUE, YELLOW))
// GREEN 
```

2.3.5 스마트 캐스트 : 타입 검사와 타입 캐스트를 조합. 

: 식을 표현하는 클래스 계층. 

노드는 합계 (Sum)나 수(Num)중 하나다. Num은 항상 말단 노드지만, Sum는 자식 둘 있는 중간 노드다. 

(Sum 노드의 두 자식은 덧셈의 두 인자다. ) 

```kotlin
interface Expr
class Num(val value : Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr
```

Expr 인터페이스에는 두 가지 구현 클래스가 존재한다. 따라서 식을 평가하려면 두가지 경우를 고려해야 한다. 

- 어떤 식이 수라면 그 값을 반환한다.
- 어떤 식이 합계라면 좌항과 우항의 값을 계산한 다음에 그 두 값을 합한 값을 반환한다.

Ex) if 연쇄를 사용해 식을 계산하기

```kotlin
fun eval(e : Expr) : Int { 
		if( e is Num){
				val n = e as Num
				return n.value
		}
		if(e is Sum){
				return eval(e.right) + eval(e.left)
		}
		throw IllegalArgumentException("Unkown expression")
}

println(eval(Sum(Sum(Num(1), Num(2)), Num(4))) //7 
```

자바에서는 어떤 변수의 타입을 instanceof로 확인한 다음에 그 타입에 속한 멤버에 접근하기 위해서는 명시적으로 변수 타입을 캐스팅해야 한다. 

코틀린에서는 프로그래머 대신 컴파일러가 캐스팅을 해준다. 

**2.3.6 리팩토링 : if를 when으로 변경**

Ex) 값을 만들어내는 if식

```kotlin
fun eval(e : Expr) : Int = 
		if(e is Num){
				e.value

		}else if(e is Sum){
				eval(e.right) + eval(e.left)
		}else{
				throw IlegalArgumentException("Unkown expression")
		}

println(eval(Sum(Num(1), Num(2)))
// 3
```

Ex) if 중첩 대신 when 사용하기

```kotlin
fun eval(e :Expr) : Int = 
		when(e) {
				is Num -> 
							e.value
				is Sum ->
						  eval(e.right) + eval(e.left)

				else ->
							throw IllegalArgumentException("Unkown expression")
		}
```

**2.3.7 if와 when의 분기에서 블록 사용**

Ex) 분기에 복잡한 동작이 들어가 있는 when 사용하기.

```kotlin
fun evalWithLogging(e : Expr) : Int = 
		when (e) {
				is Num -> {
						println("num : ${e.value}")
						e.value

				}
				is Sum -> {
						val left = evalWithLogging(e.left)
						val right = evalWithLogging(e.right)
						println("sum : $left + $right")
						left + right
				}
				else -> throw IllegalArgumentException("Unkown expression")
		}

println(evalWithLogging(Sum(Sum(Num(1), Num(2)), Num(4)))
// num : 1
// num : 2
// sum : 1 + 2
// num : 4
// sum : 3 + 4
// 7
```

### 2.4 대상을 이터레이션 : while과 for 루프

**2.4.1 while 루프**

: 코틀린에는 while 과 do-while 루프가 있다. 두 루프의 문법은 자바와 다르지 않다. 

**2.4.2 수에 대한 이터레이션 : 범위와 수열**

Ex) when을 사용해 피즈버즈 게임 구현하기.

```kotlin
fun fizzBuzz(i : Int) = when {

		i % 15 == 0 -> "FizzBuzz"
		i % 3 == 0 -> "Fizz"
		i % 5 == 0 -> "Buzz"
		else -> "$i"

}

for(i in 1..100){
		.print(fizzBuzz(i))
		...}
}
```

Ex) 증가 값을 갖고 범위 이터레이션하기

```kotlin
for(i in 100 downTo 1 step 2) {
		print(fizzBuzz(i))
}

Buzz 98 Fizz 94 42 FizzBuzz 88...
```

**2.4.3 맵에 대한 이터레이션**

: 앞 절에서 컬렉션에 대한 이터레이션을 위해 for .. in 루프를 자주 쓴다고 말했다. 

Ex) 맵을 초기화하고 이터레이션하기.

```kotlin
val binaryReps = TreeMap<Char, String>() // 키에 대한 정렬하기 위해 TreeMap을 사용한다. 

for(c in 'A' ..'F'){
		val binary = Integer.toBinaryString(c.toInt())
		binaryReps[c] = binary
}

for((letter, binary) in binaryReps){
		println("$letter = $binary")
}
```

**2.4.4 in으로 컬렉션이나 범위의 원소 검사.**

Ex) in을 사용해 값이 범위에 속하는지 검사하기.

```kotlin
fun isLetter(c: Char) = c in 'a'...'z'|| c in 'A'.. 'Z'
fun isNotDigit(c: Char) = c !in '0'..'9'

println(isLetter('q'))
true

println(isNotDigit('x'))
true
```

Ex) when에서 in 사용하기

```kotlin
fun recognize(c: Char) = when(c){
		in '0'..'9' -> "It's a digit!"
		in 'a'..'z', in 'A'..'Z' -> "It's a letter!"
		else -> "I don't know..""
}

println(recognize('8'))
//It's a digit!
```

### 2.5 코틀린의 예외 처리.

: 코틀린의 예외처리는 자바나 다른 언어의 예외 처리와 비슷하다. 함수는 정상적으로 종료할 수 있지만 오류가 발생하면 예외를 던질 수 있다. 

```kotlin
if(percentage !in 0..100){
		throw IllegalArgumentException(
				"A percentage value must be between 0 and 100: $percentage")
		)
}
```

```kotlin
val percentage = 
		if(number in 0..100)
			number
		else
			throw IllegalArgumentException(
					"A percentage value must be between 0 and 100:$number")
	
```

### 2.5.1 try, catch, finally

Ex) 자바와 마찬가지로 try 사용하기

```kotlin
fun readNumber(reader: BufferedReader) : Int?{
		try{
				val line = reader.readLine()
				return Integer.parseInt(line)
		}
		catch(e : NumberFormatException){
				return null
		}
		finally {
				reader.close()
		}

}

val reader = BufferedReader(StringReader("239"))
println(readNumber(reader)
//239
```

### 2.5.2 try를 식으로 사용.

Ex) try 를 식으로 사용하기

```kotlin
fun readNumber(reader : BufferedReader){
		val number = try { 
				Integer.parseInt(reader.readLine())

		}catch(e : NumberFormatException){
				return 
		}
		println(number)
}

val reader = BufferedReader(StringReader("not a number"))
readNumber(reader)
```

Ex) catch에서 값 반환하기

```kotlin
fun readNumber(reader : BufferedReader){
		val number = try{
				Integer.parseInt(reader.readLine())
		}catch(e: NumberFormatException){
				null
		}
		
		println(number)
}

val reader = BufferedReader(StringReader("not a number"))
readNumber(reader)

//null
```