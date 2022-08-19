코틀린의 클래스와 인터페이스는 자바 클래스, 인터페이스와 약간 다르다.

예를 들어 인터페이스에 프로퍼티 선언이 들어갈 수 있다. 

코틀린은 기본적으로 final이며 public 이다. 

### 4.1 클래스 계층 정의.

- 코틀린에서 클래스 계층정의하는 방식과 자바에서 정의하는 방식의 차이점.

4.1.1 코틀린 인터페이스

코틀인 인터페이스는 자바 8 인터페이스와 비슷하다. 

: 코틀린 인터페이스 안에는 추상 메서드뿐 아니라 구현이 되어 있는 메서드도 정의할 수 있다. 

리스트 4.1

```kotlin
interface Clickable{
		fun click();
}
```

위 코드는 click이라는 추상 메서드가 있는 인터페이스이다. 

리스트 4.2

```kotlin
class Button : Clickable { 
		override fun click() = println("I was clicked")
}

>> Button().click()
I was clicked
```

- 자바에서는 extends와 implements 키워드를 사용하지만 코틀린에서는 콜론(:)을 붙이고 인터페이스와 클래스 이름을 적는다.
- 자바와 동일하게 클래스는 인터페이스를 원하는 만큼 개수 제한 없이 마음대로 구현할 수 있지만, 클래스는 오직 하나만 확장할 수 있다.
- 자바와 달리 override 변경자를 반드시 붙여주어야 한다.

리스트 4.3 인터페이스 안에 본문이 있는 메서드 정의하기

```kotlin
interface Clickable { 
		fun click()
		fun showOff() = println("I'm clickable!") // default 구현이 있는 메서드. 

}
```

리스트 4.4  동일한 메서드를 구현하는 다른 인터페이스 정의

```kotlin
interface Focusable { 
		fun setFocus(b: Boolean) = 
			println("I ${if (b) "got" else "lost"} focus.")
		fun showOff() = println("I'm a focusable!")
}
```

위 소스는 클래스가 구현하는 두 상위 인터페이스에 정의된 showOff 구현을 대체할 오버라이딩 메서드를 직접 제공하지 않으면 다음과 같은 컴파일러 오류가 발생한다 .

리스트4.5 상속한 인터페이스의 메서드 구현 호출하기

```kotlin
class Button : Clickable, Focusable { 
			override fun click = println("I was clicked")
			override fun showOff(){
					super<Clickable>.showOff()
					super<Focusable>.showOff()
			}
}
```

### 4.1.2 open, final, abstract 변경자 : 기본적으로 final

: 자바에서는 final로 명시적으로 상속을 금지하는 경우를 제외하고 모든 클래스를 상속할 수 있다. 

상속이 가능하다는 편리함도 있지만, 문제가 생기는 경우도 많다. 

취약한 기반 클래스 라는 문제는 하위 클래스에 대해 가졌던 가정이 기반 클래스를 변경함으로써 깨져버린 경우에 생긴다. 

> 상속을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 상속을 금지하라.    -Effective java 中-
> 

특별히 하위 클래스에서 오버라이드하게 의도된 클래스와 메서드가 아니라면 모두 final로 만들라는 뜻이다. 

- 코틀린 또한 자바의 철학과 유사하다.
- 코틀린의 클래스와 메서드도 기본적으로 final이다.
- 상속을 허용하려면 클래스 앞에 open 변경자를 붙어야 한다.
- 그와 더불어 오버라이드를 허용하고 싶은 메서드나 프로퍼티 앞에도 open 변경자를 붙여야 한다.

리스트 4.6 열린 메서드를 포함하는 열린 클래스 정의하기

```kotlin
open class RichButton : Clickable { // 이 클래스는 열려있다. 다른 클래스가 이 클래스를 상속할 수 있다. 
		fun disable() { } // 이 함수는 파이널이다. 하위 클래스가 이 메서드를 오버라이드 할 수 있다. 
		open fun animate() {} // 이 함수는 열려 있다. 하위 클래스에서 이 메서드를 오버라이드해도 된다. 
		override fun click() {} // 이 함수는 열려있는 메서드를 오버라이드한다. 오버라이드한 메서드는 기본적으로 열려 있다. 

}
```

리스트4.7 오버라이드 금지하기

```kotlin
open class RichButton : Clickable { 
		final override fun click(){}
}
```

오버라이드 하지 못하게 금지하려면 메서드 앞에 final 을 명시해주면 된다. 

- 코틀린에서도 자바처럼 abstract 키워드를 이용할 수 있다.
- abstract로 선언한 추상클래스는 인스턴스화 할 수 있다.
- 추상 멤버는 항상 열려 있기 때문에 open 변경자를 명시할 필요가 없다.

리스트 4.8 추상 클래스 정의하기

```kotlin
abstract class Animated {  // 이 클래스는 추상클래스이다. 인스턴스를 만들 수 없다. 
		abstract fun animate() // 이 함수는 추상 함수다. 이 함수에는 구현이 없다. 하위 클래스에서는 이 함수를 반드시 오버라이드해야 한다. 
		open fun  stopAnimating(){

		}

		fun animateTwice(){
		}
}
```

인터페이스 멤버의 경우 final, open, abstract를 사용하지 않는다. 

표4.1. 클래스 내에서 상속 제어 변경자의 의미

| 변경자 | 이 변경자가 붙은 멤버는… | 설명. |
| --- | --- | --- |
| final | 오버라이드할 수 없음.  | 클래스 멤버의 기본 변경자이다.  |
| open | 오버라이드할 수 있음.  | 반드시 open을 명시해야 오버라이드할 수 있다.  |
| abstract | 반드시 오버라이드 해야함.  | 추상 클래스의 멤버에만 이 변경자를 붙일 수 있다. 추상 멤버에는 구현이 있으면 안된다.  |
| override | 상위 클래스나 상위 인스턴스의 멤버를 오버라이드하는 중.  | 오버라이드하는 멤버는 기본적으로 열려있다. 하위 클래스의 오버라이드를 금지하려면 final을 명시해야 한다.  |

### 4.1.3 가시성 변경자 : 기본적으로 공개.

표 4.2 코틀린의 가시성 변경자. 

| 변경자 | 클래스 멤버 | 최상위 선언 |
| --- | --- | --- |
| public (기본 가시성임) | 모든 곳에서 볼 수 있다.  | 모든 곳에서 볼 수 있다.  |
| internal | 같은 모듈 안에서만 볼 수 있다  | 같은 모듈 안에서만 볼 수 있다.  |
| protected | 하위 클래스 안에서만 볼 수 있다.  | (최상위 선언에 적용할 수 없음) |
| private | 같은 클래스 안에서만 볼 수 있다.  | 같은 파일 안에서만 볼 수 있다 . |

아래는 giveSpeech 함수 안의 각 줄은 가기성 규칙을 위반한 예이다. 

```kotlin
internal open class TalkativeButton : Focusable { 
		private fun yell() = println("Hey!")
		protected fun whisper() = println("Let's talk!")

}

fun TalkativeButton.giveSpeech(){  // 오류 "public" 멤버가 자신의 "internal" 수신 타입인 "TalkativeButton"을 노출함.
		yell()   // 오류 "yell" 에 접근할 수 없음 : "yell"은 "TalkativeButton"의 private 멤버임
		whisper()  // 오류 "whisper" 에 접근할 수 없음 : "whisper"는 "TalkativeButton"의 "protected" 멤버임. 
}
```

여기서 컴파일 오류를 없애려면 giveSpeech 확장 함수의 가시성을 internal로 바꾸거나, TalkativeButton 클래스의 가시성을 public 으로 바꿔야 한다. 

`자바에서는 같은 패키지 안에서 proctected 멤버에 접근할 수 있지만, 코틀린에서는 그렇지 않다.`

### 4.1.4 내부 클래스와 중첩된 클래스 : 기본적으로 중첩 클래스

: 자바처럼 코틀린에서도 클래스 안에 다른 클래스를 선언할 수 있다. 

- 자바와의 차이는 코틀린의 중첩 클래스는 명시적으로 요청하지 않는 한 바깥쪽 클래스 인스턴스에 대한 접근 권한이 없다는 점이다.

리스트 4.9 직렬화할 수 있는 상태가 있는 뷰 선언

```kotlin
interface State : Serializable 

interface View { 
		fun getCurrentState() : State
		fun restoreState(state: State){}
}
```

리스트 4.10 자바에서 내부 클래스를 사용해 View 구현하기

```java
/* 자바 */
public class Button implements View { 
		@Override
		public State getCurrentState(){
				return new ButtonState();
		}
		@Override
		public void restoreState(State state){ /*...*/ }
		public class ButtonState implements State { /*...*/ }
}
```

위 코드는 `java.NotSerializableException` : Button 이라는 오류가 발생한다. 

이 문제를 해결하려면 ButtonState를 static 클래스로 선언해야 한다. 

자바에서 중첩 클래스를 static으로 선언하면 그 클래스를 둘러싼 바깥쪽 클래스에 대한 묵시적인 참조가 사라진다. 

리스트 4.11 중첩 클래스를 사용해 코틀린에서 View 구현하기

```kotlin
class Button : View { 
		override fun getCurrentState() : State = ButtonState()

		override fun restoreState(state : State){/*...*/}
		class ButtonState : State { /*...*/ }
}
```

표4.3. 자바와 코트린의 중첩 클래스와 내부 클래스의 관계

| 클래스 B안에 정의된 클래스 A | 자바에서는 | 코트린에서는 |
| --- | --- | --- |
| 중첩 클래스 (바깥쪽 클래스에 대한 참조를 저장하지 않음 )  | static class A | class A |
| 내부 클래스 (바깥쪽 클래스에 대한 참조를 저장함) | class A | inner class A |

코틀린에서는 바깥쪽 클래스를 가리키는 참조를 표기하는 방법도 자바와는 다르다. 

내부 클래스 Inner 안에서 바깥쪽 클래스 Outer의 참조에 접근하려면 this@Outer라고 써야 한다. 

```kotlin
class Outer { 
		inner class Inner { 
				fun getOuterReference() : Outer = this@Outer
		}
}
```

### 4.1.5 봉인된 클래스 : 클래스 계층 정의시 계층 확장 제한.

리스트 4.12 인터페이스 구현을 통해 식 표현하기

```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e : Expr) : Int = 
		when(e) {
				is Num -> e.value
				is Sum -> eval(e.right) + eval(e.left)
				else -> 
						throw IllegalArgumentException("Unknown expression")
		}

```

코틀린 컴파일러는 when을 사용해 Expr 타입의 값을 검사할 때 꼭 디폴트 분기인 else 분기를 덧붙이게 강제한다. 

항상 디폴트 분기를 추가하는게 편하지 않을 뿐더러 디폴트 분기가 있으면 이런 클래스 계층에 새로운 하위 클래스를 추가하더라도 컴파일러가 when 이 모든 경우를 처리하는지 제대로 검사할 수 없다. 

코틀린은 이런 문제에 대한 해법을 제공하는데 sealed 클래스가 바로 그 것이다. 

리스트 4.13 sealed 클래스로 식 표현하기

```kotlin
sealed class Expr {  // 기반 클래스를 sealed로 봉인한다. 
		class Num(val value : Int) : Expr()
		class Num(val left : Expr, val right : Expr) : Expr()

}

fun eval(e: Expr) : Int = 
			when(e){        // when 식의 모든 하위 클래스를 검사하므로 별도의 else 분기가 없어도 된다. 
					is Expr.Num -> e.value
					is Expr.Sum -> eval(e.right) + eval(e.left)
			}
```

when 식에서 sealed 클래스의 모든 하위클래스를 처리한다면 디폴트 분기가 필요 없다. 

sealed로 표시된 클래스는 자동으로 open임을 기억하자. 

### 4.2 뻔하지 않은 생성자와 프로퍼티를 갖는 클래스 선언

- 자바에서는 생성자를 하나 이상 생성할 수 있다.
- 코틀린도 비슷하지만 한가지 다른 점이 있다.
- 코틀린은 주 생성자와 부 생성자로 구분한다.  또한 코틀린에서는 초기화 블록을 통해 초기화 로직을 추가할 수 있다.

4.2.1 클래스 초기화 : 주 생성자와 초기화 블록. 

```kotlin
class User(val nickname: String)
```

```kotlin
class User constructor(_nickname : String) { // 파라미터가 하나만 있는 주 생성자. 
		val nickname : String
			init { // 초기화
				nickname = _nickname
		}
}
```

- contructor : 주생성자나 부 생성자 정의를 시작할 때 사용한다.
- init : 초기화 블록을 시작한다.

( 초기화 블록에는 클래스의 객체가 만들어 질 때 실행된 초기화 코드가 들어간다. ) 

- 초기화 블록은 주 생성자와 함께 사용된다.
- 주 생성자는 제한적이기 때문에 별도의 코드를 포함할 수 없으므로 초기화 블록이 필요하다.

```kotlin
class User(_nickname : String){ // 파라미터가 하나뿐인 주 생성자
		val nickname = _nickname // 프로터피를 주 생성자의 파라미터로 초기화한다.
}
```

```kotlin
class User(val nickname: String) // val 은 이 파라미터에 상응하는 프로퍼티가 생성된다는 뜻이다.
```

```kotlin
class User(val nickname: String, val isSubscribed : Boolean = true)
```

> 모든 생성자 파라미터에 디폴트 값을 지정하면 컴파일러가 자동으로 파라미터가 없는 생성자를 만들어준다.
> 

클래스에 기반 클래스가 있다면 주 생성자에서 기반 클래스의 생성자를 호출할 필요가 있다. 

```kotlin
open class User(val nickname: String){ ... }
class TwitterUser(nickname: String) : User(nickname){ ... }
```

```kotlin
open class Button // 인자가 없는 디폴트 생성자가 만들어진다.
```

### 4.2.2 부 생성자 : 상위 클래스를 다른 방식으로 초기화

일반적으로 코틀린에서는 생성자가 여럿 있는 경우가 자바보다 훨씬 적다.

> 인자에 대한 디폴트 값을 제공하기 위해 부 생성자 여럿 만들지 말라. 대신 파라미터의 디폴트 값을 생성자 시그니처에 직접 명시하라.
> 

```kotlin
open class View {

		  constructor(ctx : Context){
				// 코드
			}
			constructor(ctx : Context, attr : AttributeSet){
				// 코드
			}

}
```

이 클래스를 확장하면서 똑같이 부 생성자를 정의할 수 있다. 

```kotlin
class MyButton : View { 
		constructor(ctx : Context)
				:super(ctx){
				// ...	
		}
		constructor(ctx: Context, attr: AttributeSet)
				:super(ctx, attr)
				// ...
		}
}
```

자바와 마찬가지로 생성자에서 this()를 통해 클래스를 자신의 다른 생성자를 호출할 수 있다.

```kotlin
class MyButton : View { 
		constrcutor(ctx : Context) : this(ctx, MY_STYLE) {
				// ...
		}
		constructor(ctx : Context, attr: AttributeSet):super(ctx, attr){
				// ....
		}
}
```

### 4.2.3 인터페이스에 선언된 프로퍼티 구현

: 코틀린에서는 인터페이스에 추상 프로퍼티 선언을 넣을 수 있다.

```kotlin
interface User { 
		val nickname : String
}
```

리스트4.14 인터페이스의 프로퍼티 구현하기 

```kotlin
class PrivateUser(override val nickname : String) : User
class SubscribingUser(val email: String) : User  {
		override val nickname: String 
				get() = email.substringBefore('@')
}

class FacebookUser(val accountId: Int) : User { 
		override val nickname = getFacebookName(accountId)
}

>> println(PrivateUser("test@kolinlang.org").nickname)
test@kolinlang.org
>> println(SubscribingUser("test@kolinlang.org").nickname)
test
```

인터페이스에는 추상 프로퍼티뿐 아니라 게터와 세터가 있는 프로퍼티를 선언할 수도 있다.

물론 그런 게터와 세터는 뒷받침하는 필드를 참조할 수 없다

```kotlin
interface User { 
		val email : String
		val nickname : String
				get() = email.substringBefore('@') 
}
```

### 4.2.4 게터와 세터에서 뒷바침하는 필드에 접근

지금까지 프로퍼티의 두가지 유형에 대해 살펴봤다.

: 이제는 이 두 유형을 조합해서 어떤 값을 저장하되 그 값을 변경하거나 읽을 때마다 정해진 로직을 실행하는 유형의 프로퍼티를 만드는 방법을 살펴보자.

리스트4.15 세터에서 뒷받침하는 필드 접근하기

```kotlin
class User(val name : String){
		var address : String = "unspecified"
			set(value: String){
					println("""
							Address was changed for $name:
							"$field" -> "$.value".""".trimIndent())
					field = value
			}

}

>> val user = User("Alic")
>> user.address = "Elsenhemerstrasse 47, 80687 Muencheen"
Address was changed for Alice:
"unspecified" -> "Elsenheimerstrasse 47, 80687 Muenchen".
```

### 4.2.5 접근자의 가시성 변경

리스트4.16 비공개 세터가 있는 프로퍼티 선언하기

```kotlin
class LenghCounter { 
		var counter : Int = 0 
				private set
		fun addWord(word: String){
				counter += word.length
		}

}
```

```kotlin
>> val lenghCounter = LenghCounter()
>> lenghCounter.addWord("Hi!")
>> println(lengthCounter.counter)
3
```

### 4.3 컴파일러가 생성한 메서드 : 데이터 클래스와 클래스 위임.

자바 플랫폼에서는 클래스가 equals, hashCode, toString 등의 메서드를 구현해야한다. 

코틀린에서는 이런 메서드를 기계적으로 생성하는 작업을 보이지 않는 곳에서 해준다. 따라서 필수 메서드로 인한 잡음 없이 소스코드를 깔끔하게 유지할 수 있다.

### 4.3.1 모든 클래스가 정의해야 하는 메서드

- 자바와 마찬가지로 코틀린 클래스도 toString, equals, hashCode 등을 오버라이드할 수 있다.
- 각각이 어떤 메서드이고 어떻게 그런 메서드를 정의해야 하는지 살펴보자.

리스트 4.17 Client 클래스의 초기 정의

```kotlin
class Client (val name : String, val postalCode : Int)
```

- **문자열 표현: toString()**
- **객체의 동등성 : equals()**
- **해시 컨테이너 : hashCode()**

리스트 4.18에 toString() 구현하기

```kotlin
class Client(val name: String, val postalCode : Int){
		override fun toString() = "Client(name = $name, postalCode = $postalCode)"
}

>> val client1 = Client("오현석", 4122)
>> println(client1)
Client(name=오현석, postalCode=4122)
```

```kotlin
val client1 = Client("오현석", 4122)
val client2 = Client("오현석", 4122)
println(client1 == client2)
false
```

리스트 4.19 Client에 equals() 구현하기

```kotlin
class Client(val name : String, val postalCode: Int){

		override fun equals(other: Any?) : Boolean {
				if(other == null || other !is Client)
						return false
				return name == other.name && postalCod == other.postalCode
				
		}
		override fun toString() = "Client(name=$name, postalCode=$postalCode)"
}
```

- 코틀린의 is 검사는 자바의 instanceof와 같다.

자바에서는 equals를 오버라이드할 때 반드시 hashCode도 함께 오버라이드해야 한다. 

```kotlin
val processed = hashSetOf(Client("오현석", 4122))
println(processed.contains(Client("오현석", 4122)))
>> false
```

위 코드는 false 가 출력되는데 그 이유는 hashCode 메서드를 정의하지 않았기 때문이다.

> JVM 언어에서는 hashCode가 지켜야 하는 “equals()가 true를 반환하는 두 객체는 반드시 같은 hashCode()를 반환해야 한다.라는 제약이 있는데 Client는 이를 어기고 있다.
> 

위 예제는 두 Client 인스턴스는 해시 코드가 다르기 때문에 두 번째 인스턴스가 집합 안에 들어있지 않다고 판단한다. 해시 코드가 다를 때 equals가 반환하는 값은 판단 결과에 영향을 미치지 않는다. 

즉,  원소 객체들이 해시 코드에 대한 규칙을 지키지 않는 경우 HashSet은 제대로 작동하지 않는다.

**리스트 4.20 Client에 hashCode 구현하기** 

```kotlin
class Client(val name: String, val postalCode : Int){
		...
		override fun hashCode() : Int = name.hashCode() * 31 + postalCode
}
```

### 4.3.2 데이터 클래스 : 모든 클래스가 정의해야 하는 메서드 자동 생성

어떤 클래스가 데이터를 저장하는 역할만을 수행한다면 toString, equals, hashCode를 반드시 오버라이드해야 한다. 

data 라는 변경자를 클래스 앞에 붙이면 필요한 메서드를 컴파일러가 자동으로 만들어준다 . data 변경자가 붙은 클래스를 데이터 클래스라고 부른다. 

리스트 4.21 Client 를 데이터 클래스로 선언하기

```kotlin
data class Client(val name : String, val postalCode :Int)
```

데이터 클래스와 불변성 : copy() 메서드

```kotlin
class Client(val name: String, val postalCode : Int){
		...
		fun copy(name:String = this.nam,
							postalCode: Int = this.postalCode) = 
				Client(name, postalCode)
}

>> val lee = Client("이계영", 4122)
>> println(lee.copy(postalCode = 4000))
Client(name=이계영, postalCode = 4000)
```

### 4.3.3 클래스 위임 : by 키워드 사용

- 대규모 객체지향 시스템을 설계할 때 시스템을 취약하게 만드는 문제는 보통 구현 상속에 의해 발생한다.
- 하위 클래스가 상위 클래스에 대해 갖고 있던 가정이 꺠져서 코드가 정상적으로 작동하지 못하는 경우가 생길 수 있다 .
- 코틀린을 설계하면서 우리는 이런 문제를 인식하고 기본적으로 클래스를 final로 취급하기 로 결정하였다.
- 종종 상속을 허용하지 않는 클래스에 새로운 동작을 추가해야 할 떄가 있는데 이 때 데코레이터 패턴을 사용한다.
- 데코레이터 패턴은 코드가 상당히 많이 필요하다.
- 이런 문제를 해결하기 위해 코틀린에서는 일급 시민 기능을 지원한다.
- 인터페이스를 구현할 때 by 키워드를 통해 그 인터페이스에 대한 구현을 다른 객체에 위임중이라는 사실을 명시할 수 있다.

리스트 4.22 클래스 위임 사용하기

```kotlin
class CountingSet<T>(
		val innerSet : MutableCollection<T> = HashSet<T>()
) : MutableCollection<T> by innerSet { // MutableCollection 의 구현을 innerSet에게 위임한다.
		var objectsAdded = 0
		override fun add(element : T) : Boolean {
				objectsAdded++
				return innerSet.add(element)
		}
		override fun addAll(c: Collction<T>):Boolean { 
				objectsAdded += c.size
				return innerSet.addAll(c)
		}
}

>> val cset = CountingSet<Int>()
>> cset.addAll(listOf(1,1,2))
>> println("${cset.objectsAdded} objects were added, ${cset.size} remain")
3 objects were added, 2 remain
```

### 4.4 object 키워드 : 클래스 선언과 인스턴스 생성

: 코틀린에서 object 인스턴스를 사용하는 여러가지 상황

- 객체 선언 : 싱글턴을 정의하는 방법 중 하나이다.
- 동반 객체 : 인스턴스 메서드는 아니지만 어떤 클래스와 관련 있는 메서드와 팩토리 메서드를 담을 때 쓰인다. 동반 객체 메서드에 접근할 때는 동반 객체가 포함된 클래스의 이름을 사용할 수 있다.
- 객체 식은 자바의 무명 내부 클래스 대신 쓰인다.

### 4.4.1 객체 선언 : 싱글턴을 쉽게 만들기

- 객체지향 시스템 설계를 하다보면 인스턴스가 하나만 필요한 클래스가 유용한 경우가 많다.
- 자바에서는 보통 클래스의 생성자를 privateㅇ로 제한하고 정적인 필드에 그 클래스의 유일한 객체를 저장하는 싱글턴 패턴을 통해 이를 구현한다.

급여 대장 예)

```kotlin
object Payroll { 
		val allEmployees = arrayListOf<Person>();
		fun calculateSalary(){
				for(person in allEmployees){
					...
				}
		}
}
```

리스트 4.23 객체 선언을 사용해 Comparator 구현하기

```kotlin
object CaseInsensitiveFileComparator : Comparator<File>{
		override fun compare(file1: File, file2: File) : Int {
				return file1.path.compareTo(file2.path, ignoreCase = true)
		}
}

>> println(CaseInsensitiveFileComparator.compare(
...     File("/User"), File("/user")))
0
```

```kotlin
>> val files = listOf(Fil("/Z"), File("/a"))
>> println(files.sortedWith(CaseInsensitiveFileComparator))
[/a, /Z]
```

리스트 4.24 중첩 객체를 사용해 Comparator 구현하기

```kotlin
data class Person(val name : String){
		object NameComparator : Comparator<Person> {
				override fun compare(p1: Person, p2: Person) : Int = 
						p1.name.compareTo(p2.name)
		}
}

>> val persons = listOf(Person("Bob"), Person("Alice"))
>> println(persons.sortedWith(Person.NameComparator))
[Person(name=Alice), Person(name = Bob)]
```

클래스 안에 중첩된 객체 중에서도 독특한 객체가 있는데 그 객체는 바로 동반 객체이다.

### 4.4.2 동반 객체 : 팩토리 메서드와 정적 멤버가 들어갈 장소.

- 코틀린 클래스 안에는 정적인 멤버가 없다.
- 코틀린 언어는 자바 static 키워드를 지원하지 않는다.
- 그 대신 코틀린에서는 패키지 수준의 최상위 함수와 객체 선언을 활용한다.

```kotlin
class A { 
		companion object { 
			fun bar(){
					println("Companion object called")
			}
		}
}

>> A.bar()
Companion object called
```

리스트 4.25 부 생성자가 여럿 있는 클래스 정의하기

```kotlin
class User { 
		val nickname : String
		constructor(email: String){ // 부생성자
				nickname = email.substringBefore('@')
		}
		constructor(facebookAccountId : Int){  // 부생성자
				nickname = getFacebookName(facebookAcoountId)
		}
		
}
```

리스트 4.26 부 생성자를 팩토리 메서드로 대신하기

```kotlin
class User private constructor(val nickname : String){
		companion object { 
				fun newSubscribingUser(email : String) = 
							User(email.substringBefore('@'))
				fun newFacebookUser(accountId : Int) = 
							User(getFacebookName(accountId))
		}
}
```

```kotlin
>> val subscribingUser = User.newSubscribingUser("bob@gmail.com")
>> val facebookUser = User.newFacebookUser(4)
>> println(subscribingUser.nickname)
bob
```

### 4.4.3 동반 객체를 일반 객체처럼 사용

: 동반 객체는 클래스 안에 정의된 일반 객체이다. 

리스트 4.27 동반 객체에 이름 붙이기

```kotlin
class Person(val name : String){
		companion object Loader {
				fun fromJSON(jsonText: String) : Person = ...
		}
}

>> person = Person.Loader.fromJSON("{name : 'Dmitry'}")
>> person.name
Dmitry
>> person2 = Person.fromJSON("{name : 'Brent'}")
>> person2.nam
Brent
```

**동반 객체에서 인터페이스 구현**

리스트 4.28 동반 객체에서 인터페이스 구현하기

```kotlin
interface JSONFactory<T> {
		fun fromJSON(jsonText: String) : T
}

class Person(val name : String){
		companion object : JSONFactory<Person>{
				override fun fromJSON(jsonText : String) : Person = ... // 동반 객체가 인터페이스를 구현한다. 
		}

}
```

**동반 객체 확장.**

리스트 4.29 동반 객체에 대한 확장 함수 정의하기 

```kotlin
// 비즈니스 로직 모듈
class Person(val firstName : String, val lastName : String){
		companion object {  // 비어있는 동반 객체를 선언한다.
		}
}

// 클라이언트 서버 통신 모듈
fun Person.Companion.fromJSON(json:String) : Person {  // 확장 함수를 선언한다.
		...
}

val p = Person.fromJSON(json)
```

### 4.4.4 객체 식 : 무명 내부 클래스를 다른 방식으로 작성.

- object 키워드를 싱글턴과 같은 객체를 정의하고 그 객체에 이름을 붙일 때만 사용하지 않는다.
- 무명 객체를 정의할 때도 object 키워드를 쓴다.
- 무명 객체는 자바의 무명 내부 클래스를 대신한다.
- 자바에서 흔히 무명 내부 클래스로 구현하는 이벤트 리스너를 코틀린에서 구현해 보자.

리스트 4.30 무명 객체로 이벤트 리스너 구현하기

```kotlin
window.addMouseListener (
		object : MouseAdapter(){
				override fun mouseCliked(e: MouseEvent){
						//...
				}

				override fun mouseEntered(e: MouseEvent){
						//...
				}
		}

)
```

> 객체 선언과 달리 무명 객체는 싱글턴이 아니다. 객체 식이 쓰일 때마다 새로운 인스턴스가 생성된다.
> 

리스트 4.31 무명 객체 안에서 로컬 변수 사용하기

```kotlin
fun countClicks(window: Window){
		var clickCount = 0  // 로컬 변수를 정의한다. 
		window.addMouseListener(object : MouseAdapter){
				override fun mouseCliked(e : MouseEvent){
						clickCount++    // 로컬 변수의 값을 변경한다.
				}	
		}
}
```

---