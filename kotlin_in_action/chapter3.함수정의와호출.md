# 3장. 함수 정의와 호출.

- 컬렉션, 문자열 정규식을 다루기 위한 함수.
- 이름 붙인 인자, 디폴트 파라미터 값, 중위 호출 문법 사용.
- 확장 함수와 프로퍼티를 사용해 자바 라이브러리 적용.
- 최상위 및 로컬 함수와 프로퍼티를 사용해 코드 구조화

---

### 3.1. 코틀린에서 컬렉션 만들기.

```kotlin
val set = hashSetOf(1, 7, 53) 
```

```kotlin
val list = arrayListOf(1, 7, 53)
val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```

```kotlin
>> println(set.javaClass) // javaClass는 자바 getClass()에 해당하는 코틀린 코드다. 
class.java.util.HashSet

>> println(list.javaClass) 
class.java.util.ArrayList

>> println(map.javaClass)
class.java.util.HashMap
```

위 결과는 코틀린이 자신만의 컬렉션 기능을 제공하지 않는다는 뜻이다. 

코틀린이 자체 컬렉션을 제공하지 않는 이유는 뭘까? 표준 자바 컬렉션을 활용하면 자바 코드와 상호작용하기가 훨씬 더 쉽다. 

자바에서 코틀린 함수를 호출하거나 코틀린에서 자바 함수를 호출할 때 자바와 코틀린 컬렉션을 서로 변환할 필요가 없다. 

> 코틀린 컬렉션과 자바 컬렉션은 똑같은 클래스이다. 하지만 코틀린에서는 자바보다 더 많은 기능을 쓸 수 있다.
> 

```kotlin
>> val strings = listOf("first", "second", "fourteenth")

>> println(strings.last())
fourteenth

>> val numbers = setOf(1, 14, 2) 

>> println(numbers.max())
14
```

---

### 3.2 함수를 호출하기 쉽게 만들기.

자바 컬렉션에는 디폴트 toString 구현이 들어있다.

```kotlin
>> val list = listOf(1,2,3)
>> println(list)  // toString() 호출.
[1,2,3]
```

리스트 joinToString 함수는 컬렉션의 원소를 StringBuilder 의 뒤에 덧붙인다. 

이때 원소 사이에 구분자를 추가하고, StringBuilder의 맨 앞과 맨 앞과 맨 뒤에는 접두사와 접미사를 추가한다. 

**리스트3.1 joinToString()  함수의 초기 구현.** 

```kotlin
fun <T> joinToString(
		collection: Collection<T>,
		separator : String, 
		prefix : String, 
		postfix : String
) : String (
		val result = StringBuilder(prefix)
		for((index, element) in collection.withIndex()){
				if(index >0) result.append(separator)
				result.append(element)
		}
		result.append(postfix)
		return result.toString()
)
```

이 함수는 제네릭하다. 즉 이 함수는 어떤 타입과 값을 원소로 하는 컬렉션이든 처리할 수 있다. 

제네릭 함수의 문법은 자바와 비슷하다.

```kotlin
>> val list = listOf(1, 2, 3)
>> println(joinToString(list, "; ", "(", ")"))
(1; 2; 3) 
```

**3.2.1 이름 붙인 인자**

```kotlin
joinToString(collection, "", "", ".")
```

이런 문제는 특히 불리언 플래그 값을 전달해야 하는 경우 흔히 발생한다. 

이를 해결하기 위한 일부 자바 코딩 스타일에서는 불리언 대신 enum 타입을 사용하라고 권장한다. 

```kotlin
/* 자바 */
joinToString(collection, /* separator */ " ", /* prefix */ " ", /* postfix */ ".") 
```

```kotlin
joinToString(collection, separator = " ", prefix = " ", postfix = ".")
```

**3.2.2 디폴트 파라미터 값**

: 자바에서는 일부 클래스에서 오버로딩한 메서드가 너무 많아진다는 문제가 있다. 

**코틀린에서는 함수 선언에서 파라미터의 디폴트 값을 지정할 수 있으므로 이런 오버로드 상당수를 피할 수 있다.  디폴트 값을 사용해 joinToString 함수를 개선해보자.** 

**리스트3.2 디폴트 파라미터 값을 사용해 joinToString() 정의하기.**

```kotlin
fun <T> joinToString(
		collection : Collection<T>, 
		separator: String = ", ",
		prefix: String = "",
		postfix: String = ""
) : String 
```

이제 함수를 호출할 때 모든 인자를 쓸 수 있고, 일부를 생략할 수도 있다.

```kotlin
>> joinToString(list, ", ","", "")
1, 2, 3
>> joinToString(list)   // separator, prefix, postfix 생략
1, 2, 3
>> joinToString(list, "; ") // separator를 "; "로 지정, prefix와 postfix 생략. 
1; 2; 3
```

```kotlin
>> joinToString(list, postfix =";", prefix = "# ")
# 1, 2, 3;
```

**디폴트 값과 자바**

: **자바에는 디폴트 파라미터 값이라는 개념이 없어서** 코틀린 함수를 자바에서 호출하는 경우에는 그 코틀린 함수가 디폴트 파라미터 값을 제공하다더라도 모든 인자를 명시해야 한다. 

예를 들어 joinToString에 @JvmOverloads를 붙이면 다음과 같은 오버로딩한 함수가 만들어진다. 

```kotlin
/* 자바 */
String joinToString(Collection<T> collection, String separator, String prefix, String postfix);

String joinToString(Collection<T> collection, String separator, String prefix);

String joinToString(Collection<T> collection, String separator);

String joinToString(Collection<T> collection); 

각각의 오버로딩한 함수들은 시그니처에서 생략된 파라미터에 대해 코틀린 함수의 디폴트 파라미터 값을 사용한다.
```

**3.2.3 정적인 유틸리티 클래스 없애기 : 최상위 함수와 프로퍼티**

자바를 아는 사람은 객체지향 언어인 자바에서는 모든 코드를 클래스의 메서드로 작성해야 한다는 사실을 알고 있다. 

하지만 실전에서는 어느 한 클래스에 포함시키기 어려운 코드가 많이 생긴다.  일부 연산에서는 어느 한 클래스가 둘 이상 있을 수도 있다. 

중요한 객체는 하나 뿐이지만 그 연산을 객체의 인스턴스 API 에 추가해서 API를 너무 크게 만들고 싶지 않을 수도 있다. 

그 결과 다양한 정적 메서드를 모아두는 역할만 담당하며, 특별한 상태나 인스턴스 메서드는 없는 클래스가 생겨난다. 

ex) JDK의 Collections 클래스가 전형적인 예이다. 

joinToString 함수를 strings 패키지에 직접 넣어보자. join.kt 라는 파일을 다음과 같이 작성하라 .

**리스트3.3 joinToString() 함수를 최상위 함수로 선언하기.** 

```kotlin
package strings

fun joinToString (...) : String { ... }
```

이 함수가 어떻게 실행될 수 있는 걸까? jvm이 클래스 안에 들어 있는 코드만을 실행할 수 있기 떄문에 컴파일러는 이 파일을 컴파일 할 때 새로운 클래스를 정의해준다. 

코틀린만 사용하는 경우에는 그냥 그런 클래스가 생긴다는 사실만 기억하면 된다. 하지만 함수를 자바 등의 다른 JVM 언어에서 호출하고 싶다면 코드가 어떻게 컴파일되는지 알아야 joinToString과 같은 최상위 함수를 사용할 수 있다. 

join.kt를 컴파일한 결과와 같은 클래스를 자바 코드로 써보면 다음과 같다 .

```java
/* 자바 */
package strings;

public class JoinKt { 
		public static String joinToString(...){...} 

}
```

```java
/* 자바 */
import strings.JoinKt; 

...
JoinKt.JoinToString(list, ", ", "", "");
```

**최상위 프로퍼티** 

: 함수와 마찬가지로 프로퍼티도 파일의 최상위 수준에 놓을 수 있다. 어떤 데이터를 클래스 밖에 위치시켜야 하는 경우는 흔하지는 않지만, 그래도 가끔 유용할 때가 있다. 

**ex) 연산을 수행한 횟수를 저장하는 var 프로퍼티.**

```java
var opCount = 0 // 최상위 프로퍼티를 선언한다.
fun performOperation(){
		opCount++   // 최상위 프로퍼티의 값을 변경한다. 
		//..
}

fun reportOperationCount(){
		println("Operation performed $opCount times") // 최상위 프로퍼티의 값을 읽는다 .
}
```

이런 프로퍼티의 값은 정적 필드에 저장된다. 최상위 프로퍼티를 활용해 코드에 상수를 추가할 수 있다. 

```java
val UNIX_LINE_SEPARATOR = "\n"
```

- 기본적으로 최상위 프로퍼티도 다른 모든 프로퍼티처럼 접근자 메서드를 통해 자바 코드에 노출된다.  **(val의 경우 게터, var의 경우 게터와 세터가 생긴다. )**
- 겉으론 상수처럼 보이는데 실제로는 게터를 사용해야 한다면 자연스럽지 못한다. 더 자연스럽게 사용하려면 상수를 public static final 필드로 컴파일 해야 한다.
- const 변경자를 추가하면 프로퍼티를 public static final 필드로 컴파일하게 만들 수 있다. ( 단, 원시 타입과 String 타입의 프로퍼티만 const를 지정할 수 있다.)

아래 두 코드는 동등한 바이트코드를 만들어낸다. 

```java
/* 자바 */
public static final String UNIX_LINE_SEPARATOR = "\n"
```

```kotlin
const val UNIX_LINE_SEPARATOR = "\n"
```

---

### 3.3. 메서드를 다른 클래스에 추가 : 확장 함수와 확장 프로퍼티.

- 기존 코드와 코틀린 코드를 자연스럽게 통합하는 것은 코틀린의 핵심 목표 중 하나다.
- 완전히 코틀린으로만 이뤄진 프로젝트조차도 JDK 나 안드로이드 프레임워크 또는 다른 서드파티 프레임워크 등의 자바 라이브러리를 기반으로 만들어진다.
- 또 코틀린을 기존 자바 프로젝트에 통합하는 경우에는 코틀린으로 직접 변환할 수 없거나 미처 변환하지 않은 기존 자바 코드를 처리할 수 있어야 한다.

- 이런 기존 자바 API를 재작성하지 않고도 코틀린이 제공하는 여러 편리한 기능을 사용할 수 있으면 정말 좋을 텐데 …

: 코틀린에서 확장함수(Extension function)가 그런 역할을 해준다.

확장 함수란?

어떤 클래스의 멤버 메서드인 것처럼 호출할 수 있지만 그 클래스 밖에서 선언된 함수다.

```kotlin
package string

fun String.lastChar() : Char = this.get(this.length -1)
```

- 수신 객체 타입 : 클래스 이름
- 수신 객체 : 확장 함수가 호출되는 대상이 되는 값(객체)

```kotlin
>> println("Kotlin".lastChar())
n
```

위 코드는 수신 객체 타입이 String 이고, “Kotlin”이 수신 객체이다. 

ex) 일반 메서드와 마찬가지로 확장 함수 본문에서도 this를 생략할 수 있다. 

```kotlin
package strings

fun String.lastChar() : Char = get(length -1) 
```

- 확장함수 내부에서는 일반적인 인스턴스 메서드의 내부에서와 마찬가지로 수신 객체의 메서드나 프로퍼티를 바로 사용할 수 있다.
- 하지만 확장 함수가 캡슐화를 꺠지는 않는다는 사실을 기억하자.
- 클래스 안에서만 정의된 메서드와 달리 확장 함수 안에서는 클래스 내부에서만 사용할 수 있는 비공개(private) 멤버나 보호된 (protected)멤버를 사용할 수 없다.

> 이제부터는 클래스의 멤버 메서드와 확장 함수를 모두 메서드라고 부를 것이다.
> 

**3.3.1 임포트와 확장 함수.**

: 확장함수를 사용하기 위해서는 그 함수를 다른 클래스나 함수와 마찬가지로 임포트 해야 한다.

```kotlin
import strings.lastChar

val c = "Kotlin".lastChar()
```

```kotlin
import string.*

val c = "Kotlin".lastChar()
```

as 키워드 사용

```kotlin
import strings.lastChar as last
val c = "Kotlin".last()
```

> 한 파일 안에서 다른 여러 패키지에 속해있는 이름이 같은 함수를 가져와 사용해야 하는 경우 이름을 바꿔서 임포트하면 이름 충돌을 막을 수 있다.
> 

**3.3.2. 자바에서 확장 함수 호출.** 

- 내부적으로 확장 함수는 수신 객체를 첫 번째 인자로 받는 정적 메서드다.
- 그래서 확장 함수를 호출해도 다른 어댑터 객체나 실행 시점 부가 비용이 들지 않는다.

확장 함수를 String.Util.kt 파일에 정의했다면 다음과 같이 호출할 수 있다. 

```kotlin
char c = StringUtilKt.lastChar("Java");
```

**3.3.3  확장 함수로 유티릴티 함수 정의**

이제 joinToString함수의 최종버전을 만들자.  이제 이 함수는 코틀린 라이브러리가 제공하는 함수와 거의 같아졌다.

**리스트3.4. joinToString() 를 확장으로 정의하기.** 

```kotlin
fun <T> Collection<T>.joinToString( // Collection<T>에 대한 확장 함수를 선언한다.
		separator : String = ", ",
		prefix : String = "",
		postfix : String = ""
): String {
		val result = StringBuilder(prefix)
		for((index, element) in this.withIndex())  // "this"는 수신 객체를 가리킨다. 여기서는 T 타입의 원소로 이뤄진 컬렉션이다.
				if(index >0) result.append(separator)
				result.append(element)
				result.append(postfix)			
				return result.toString()
}

>> val list = listOf(1,2,3)
>> println(list.joinToString(separator = "; ",
...     prefix =  "(", postfix = ")"))

(1; 2; 3)
```

원소로 이뤄진 컬렉션에 대한 확장을 만든다. 그리고 모든 인자에 대한 디폴트 값을 지정한다.

이제 joinToString을 마치 클래스의 멤버인 것처럼 호출할 수 있다.

```kotlin
>> val list = arrayListOf(1,2,3)
>> println(list.joinToString(" "))
1 2 3
```

확장 함수는 단지 정적 메서드 호출에 대한 문법적인 편의일 뿐이다. 그래서 클래스가 아닌 더 구체적인 타입을 수신 객체 타입으로 지정할 수도 있다. 

```kotlin
fun Collection<String>.join(
		separator : String = ", ",
		prefix : String = "",
		postfix : String = ""
) = joinToString(separator, prefix, postfix)

>> println(listOf("one", "Two", "eight").join(" ")) 
one two eight
```

이 함수를 객체의 리스트에 대해 호출할 수 없다.

```kotlin
>> listOf(1,2,8).join()
Error : Type mismatch : inferred typeis List<Int> but Collection<String> was expected 
```

**3.3.4 확장 함수는 오버라이딩 할 수 없다.**

: 코틀린의 메서드는 오버라이드도 일반적인 객체지향의 메서드 오버라이드와 마찬가지다. 하지만 확장 함수는 오버라이드 할 수 없다. 

리스트 3.5 멤버 함수 오버라이드하기.

```kotlin
open class View {
		open fun click() = println("View clicked")
}

class Button : View(){
		override fun click() = println("Button clicked")
}

>> val view : View = Button()
>> view.click()   // "view" 에 저장된 값의 실제 타입에 따로 호출할 메서드가 결정된다. 
Button clicked
```

리스트 3.6 확장 함수는 오버라이드할 수 없다.

```kotlin
fun View.showOff() = println("I'm a view!")
fun Button.showOff() = println("I'm a button!")

>> >> val view : View = Button()
>> view.showOff()
I'm a view!
```

**3.3.5 확장 프로퍼티**

 : 확장 프로퍼티를 사용하면 기존 클래스 객체에 대한 프로퍼티 형식의 구문으로 사용할 수 있는 API를 추가할 수 있다. 

리스트3.7 확장 프로퍼티 선언하기

```kotlin
val String.lastChar : Char
	 get() = get(length -1)
```

리스트 3.8 변경 가능한 확장 프로퍼티 선언하기

```kotlin
var StringBuilder.lastChar : Char
		get() = get(length -1) //프로퍼티 게터
		set(value : Char){
				this.setCharAt(length -1, value) //프로퍼티 세터. 
		}
```

확장 프로퍼티를 사용하는 방법은 멤버 프로퍼티를 사용하는 방법과 같다.

```kotlin
>> prinltn("Kotlin".lastChar)
n
>> val sh = StrinbBuilder("Kotlin?")
>> sb.lastChar = "!"
>> println(sb)
Kotlin!
```

---

### 3.4 컬렉션 처리 : 가변 길이 인자, 중위 함수 호출, 라이브러리 지원

: 코틀린의 표준 라이브러리.

- varang 키둬르를 사용하면 호출 시 인자 개수가 달라질 수 있는 함수를 정의할 수 있다.
- 중위 함수 호출 구문을 사용하면 인자가 하나뿐인 메서드를 간편하게 호출할 수 있다.
- 구조 분해 선언 을 사용하면 복합적인 값을 분해해서 여러 변수에 나눠 담을 수 있다.

**3.4.1 자바 컬렉션 API 확장.** 

리스트의 마지막 원소를 가져오는 예제와 숫자로 이뤄진 컬렉션의 최대값을 찾는 예제)

```kotlin
val strings : List<String> = listOf("first", "second", "fourteenth")
>>> strings.last()
fourteenth
>> val numbers : Collection<Int> = setOf(1, 14, 2)
>> numbers.max()
14

```

 

**3.4.2 가변 인자 함수 : 인자의 개수가 달라질 수 있는 함수 정의**

리스트를 생성하는 함수를 호출할 때 원하는 만큼 많이 원소를 전달할 수 있다. 

```kotlin
val list = listOf(2, 3, 5, 7, 11)
```

라이브러리에서 이 함수의 정의를 보면 아래와 같다. 

```kotlin
fun listOf<T>(vararg values : T) : List<T>{ ... }
```

자바에서는 배열을 그냥 넘기면 되지만 코틀린에서는 배열을 명시적으로 풀어서 배열의 각 원소가 인자로 전달되게 해야 한다. 

**기술적으로는 스프레드 연산자가 그런 작업을 해준다. 실제로는 전달하려는 배열 앞에 *를 붙이면 된다.**

```kotlin
fun main(args : Array<String>){
		val list = listOf("args : ", *args)
		println(list)
}
```

3.4.3 값의 쌍 다루기 : 중위 호출과 구조 분해 선언

맵을 만들려면 mapOf 함수를 사용한다.

```kotlin
val map = mapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```

위 코드는 중위 호출이라는 특별한 방식으로 to라는 일반메서드를 선언했다. 

함수를 중위 호출에 사용하게 허용하고 싶으면 infix 변경자를 함수 선언 앞에 추가해야 한다. 

```kotlin
infix fun Any.to(other: Any) = Pair(this, other)
```

이 to 함수는 Pair 의 인스턴스를 반환한다. 

```kotlin
val (number, name) = 1 to "one"
```

Pair의 내용으로 두 변수를 즉시 초기화 할 수 있다. **이런 기능을 구조 분해 선언(destructuring declaration)이라고 부른다.** 

**루프에서도 구조 분해 선언을 활용할 수 있다.** 

joinToString에서 본 withIndex를 구조 분해 선언과 조합하면 컬렉션 원소의 인덱스와 값을 따로 변수에 담을 수 있다. 

```kotlin
for((index, element) in collection.withIndex()){
		println("$index : $element")
}
```

to함수는 확장 함수다. to를 사용하면 타입과 관계없이 임의의 순서쌍을 만들 수 있다. 이는 to의 수신 객체가 제네릭하다는 뜻이다. 

mapOf 함수의 선언을 살표보자

```kotlin
fun <K, V> mapOf(vararg values: Pair<K, V>) : Map<K, V>
```

- listOf와 마찬가지로 mapOf도 원하는 개수만큼 인자를 전달할 수 있다.
- 하지만 mapOf같은 경우에는 각 인자가 키와 값으로 이뤄진 쌍이여야 한다.

---

### 3.5. 문자열 정규식 다루기.

: 자바와 코틀린 API 차이를 알아보자.

**3.5.1 문자열 나누기**

자바 개발자라면 String의 split 메서드를 잘 알고 있을 것이다. 

- “자바 split 메서드로는 점(.)을 사용해 문자열을 분리할 수 없다.
- “12.345-6.A”.split(”.”) 라는 호출의 결과가 [12, 345-6, A] 배열이라고 생각하는 실수를 저지르는 개발자가 많다.
- 하지만 자바의 split 메서드는 빈 배열을 반환한다.
- split의 구문 문자열은 실제로는 정규식이기 때문이다. 따라서  마침표(.)는 모든 문자를 나타내는 정규식으로 해석된다.

마침표나 대시(-)로 문자열을 분리하는 예)

```kotlin
println("12.345-6.A".split("\\.|".toRegex())) // 정규식을 명시적으로 만든다. 
[12, 345, 6, A]
```

split 확장 함수를 오버로딩한 버전 중에는 구분 문자열을 하나 이상 인자로 받는 함수가 있다. 

```kotlin
>> println("12.345-6.A".split(".", "-"))
[12, 345, 6, A]
```

이렇게 여러 문자를 받을 수 있는 코틀린 확장 함수는 자바에 있는 단 하나의 문자만 받을 수 있는 메서드를 대신한다. 

**3.5.2 정규식과 3중 따옴표를 묶은 문자열**

: 첫번째 구현은 String을 확장한 함수를 사용하고 두 번째 구현은 정규식을 사용한다. 

- 파일의 전체 경로명을 디렉터리, 파일 이름, 확장자로 구분해보자.

**리스트 3.9 String 확장 함수를 사용해 경로 파싱하기.**

```kotlin
fun parsePath(path: String) {
		val directory = path.substringBeforeLast("/")
		val fullname = path.substringAfterLast("/")
		val fileName = fullName.substringBeforeLast(".")
		val extension = fullName.substringAfterLast(".")
		println("Dir : $directory, name : $fileName, ext: $extension")
}

>> parsePath("/User/yole/kotlin-book/chapter.adoc")
Dir: /Users/yole/kotlin-book, name:chapter, ext:adoc
```

**리스트 3.10 경로 파싱에 정규식 사용하기**

```kotlin
fun parsePath(path: String){
		val regex = """.(.+)/(.+)\.(.+)""".toRegex()
		val matchResult = regex.matchEntire(path)
		if(matchResult != null){
				val (directory, filename, extension) = matchResult.destructured
				println("Dir : $directory, name: $filename, ext: $extension")
		}

}
```

**3.5.3 여러 줄 3중 따옴표 문자열**

: 3중 따옴표 문자열을 문자열 이스케이프를 피하기 위해서만 사용하지는 않는다. 

```kotlin
val tripleQuotedString = """
                            .Hello World!!
                            .It's a triple-quoted string!
                            """

println(tripleQuotedString)
//
//                            .Hello World!!
//                            .It's a triple-quoted string!
//                          

```

---

### 3.6. 코드 다듬기 : 로컬 함수와 확장.

: 코드를 작성할 때 DRY(Don’t Repeat Yourself)원칙을 피하기 쉽지 않다. 

코틀린에서는 함수에서 추출한 함수를 원 함수 내부에 중첩시킬 수 있다. 그렇게 하면 문법적인 부가 비용을 들이지 않고 깔끔하게 코드를 조작할 수 있다. 

코드 중복을 로컬 함수를 통해 제거하는 방법. 

**리스트 3.11 코드 중복을 보여주는 예제**

```kotlin
class User(val id : Int, val name : String, val address : String)

fun saveUser(user : User){
		if(user.name.isEmpty()){
				throw IllegalArgumentException(
						"Can't save user ${user.id} : empty Name")
		}
		if(user.address.isEmpty()){
				throw IllegalArgumentException(
						"Can't save user ${user.id} : empty Address")

		}
		// user를 db에 저장한다. 
}

>> saveUser(User(1, "","")
java.lang.IllegalArgumentException : Can't save user 1 : empty Name
```

리스트 3.12 로컬 함수를 사용해 코드 중복 줄이기 

```kotlin
class User(val id : Int, val name : String, val address : String)

fun saveUser(user : User){
		fun validate(user: User, value: String, fieldName: String){
				if(value.isEmpty){
						throw IllegalArgumentExcpetion(
								"Can't save user ${user.id} : empty $fieldName")
						
				}
		}

		validate(user, user.name, "Name")
		validate(user, user.address, "Address")

		// user 를 db에 저장한다. 
}
```

검증 로직 중복은 사라졌다. but User 객체를 로컬 함수에게 하나하나 전달해야 한다는 점은 아쉽다 .

로컬 함수는 자신이 속한 바깥 함수의 모든 파라미터와 변수를 사용할 수 있다. 이런 성질을 이용해 불필요한 User 파라미터를 없애보자

**리스트 3.13 로컬 함수에서 함수의 파라미터 접근하기.**

```kotlin
class User(val id: Int, val name : String, val address : String)

fun saveUser(user : User){
		fun validate(value:String, fieldName:String){
				if(value.isEmpty()){
							throw IllegalArgumentsException(
									Can't save user ${user.id} : " + "empty $fieldName")
				}
		}
		validate(user.name, "Name")
		validate(user.address, "Address")

	  // user를 데이터베이스에 저장한다. 
}
```

이 예제를 더 개선하고 싶다면 검증 로직을 User 클래스를 확장한 함수로 만들 수도 있다. 

**리스트 3.14 검증 로직을 확장 함수로 추출하기** 

```kotlin
class User(val id: Int, val name : String, val address : String)

fun User.validateBeforeSave(){
		fun validate(value : String, fieldName : String){
				if(value.isEmpty()){
						throw IllegalArgumentException(
								"Can't save user $id : empty $fieldName")
				}
		}
		validate(name, "Name")
		validate(address, "Address")
}

fun save(user:User){
		user.validateBeforeSave()

		// user를 db에 저장한다.
}
```

- 한 객체의 비공개 데이터를 다룰 필요는 없는 함수는 리스트 3.14 처럼 확장 함수로 만들면 객체.멤버처럼 수신 객체를 지정하지 않고도 공개된 멤버 프로퍼티나 메서드에 접근할 수 있다.

---

### 요약

- 코틀린은 자체 컬렉션 클래스를 정의하지 않지만 자바 클래스를 확장해서 더 풍부한 API를 제공한다.
- 함수 파라미터의 디폴트 값을 정의하면 오버로딩한 함수를 정의할 필요성이 줄어든다. 이름 붙인 인자를 사용하면 함수의 인자가 많을 때 함수 호출의 가독성을 더 향상 시킬 수 있다.
- 코틀린 파일에서 클래스 멤버가 아닌 최상위 함수와 프로퍼티를 직접 선언할 수 있다. 이를 활용하면 코드 구조를 더 유연하게 만들 수 있다.
- 확장 함수와 프로퍼티를 사용하면 외부 라이브러리에 정의된 클래스를 포함해 모든 클래스의 api를 그 클래스의 소스코드를 바꿀 필요 없이 확장할 수 있다.

확장 함수를 사용해도 실행 시점에 부가 비용이 들지 않는다.

- 중위 호출을 통해 인자가 하나 밖에 없는 메서드나 확장 함수를 더 깔끔한 구문으로 호출할 수 있다.
- 코틀린은 정규식과 일반 문자열을 처리할 때 유용한 다양한 문자열 처리 함수를 제공한다.
- 자바 문자열로 푠현하려면 수많은 이스케이프가 필요한 문자열의 경우 3중 따옴표 문자열을 사용하면 더 깔끔하게 표현할 수 있다.
- 로컬 함수를 써서 코드를 더 깔끔하게 유지하면서 중복을 제거할 수 있다.