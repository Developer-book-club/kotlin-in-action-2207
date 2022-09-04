# Overview

---

- 코틀린에서 제네릭 클래스와 함수를 선언하고 사용하는 개념은 자바와 비슷하다.
- 실체화한 타입 파라미터(reified type parameter)나 선언 지점 변성(declaration-site variance)를 설명한다.
- 실체화한 타입 파라미터 사용하면 인라인 함수 호출에서 타입 인자로 쓰인 구체적인 타입을 실행 시점에 알 수 있다.
    - 일반 클래스나 함수의 경우 타입 인자 정보가 실행 시점에서 사라지기 때문에 이런 일이 불가능하다.
- 선언 지점 변성을 사용하면 기저 타입은 같지만 타입 인자가 다른 두 제네릭 타입 `Type<A>`와 `Type<B>`가 있을 때 타입 인자 A와 B의 상위/하위 타입 관계에 따라 두 제네릭 타입의 상위/하위 타입 관계가 어떻게 되는지 지정할 수 있다.
    - `List<Any>`를 인자로 받는 함수에게 `List<Int>` 타입의 값을 전달할 수 있는지 여부를 지정
- 사용 지점 변성(user-site variance)은 같은 목표(제네릭 타입 값 사이의 상위/하위 타입 관계 지정)를 제네릭 타입 값을 사용하는 위치에서 파라미터 타입에 대한 제약을 표시하는 방식으로 달성
- 자바 와일드카드는 사용 지점 변성에 속하면 코틀린 선언 지점 변성과 같은 역할을 한다.

# 1. 제네릭 타입 파라미터

---

- 제네릭스를 사용하면 타입 파라미터(type parameter)를 받는 타입을 정의할 수 있다.
- 제네릭 타입의 인스턴스를 만들려면 타입 파라미터를 구체적인 타입 인자(type argument)로 치환해야 한다.
- 코틀린 컴파일러는 보통 타입과 마찬가지로 타입 인자로 추론할 수 있다.
    
    ```kotlin
    val authors = listOf("Dmirty", "Svetlana")
    ```
    
    - 빈 리스트를 만들 때 변수의 타입을 지정해도 되고 변수를 만드는 함수의 타입 인자를 안지정해도 된다.

<aside>
💡 자바와 달리 코틀린에서는 제네릭 타입의 인자를 프로그래머가 명시하거나 컴파일러가 추론할 수 있어야 한다. 자바의 경우 1.5버전부터 제네릭이 도입되었기 때문에 이전 버전과 호환성을 유지하기 위해 타입 인자가 없는 제네릭 타입(raw 타입)을 허용한다. 예를 들어 자바에서는 리스트 원소 타입을 지정하지 않고 List 타입의 변수를 선언할 수 있다. 코틀린은 raw 타입을 지원하지 않고 제네릭 타입의 타입 인자(직접 정의 혹은 타입 추론에 의해 자동 정의)를 항상 정의해야 한다.

</aside>

## 제네릭 함수와 프로퍼티

- 리스트를 다루는 함수를 정의하려 했을 때 모든 타입을 저장할 수 있는 제네릭 리스트를 다루는 함수를 원할 수 있다.
- 컬렉션을 다루는 라이브러리의 함수는 대부분 제네릭 함수다.
    
    ```kotlin
    fun <T> List<T>.slice(indices: IntRange): List<T>
    ```
    
    - 타입 파라미터가 수신 객체와 반환 타입에 쓰인다.
- 제네릭 고차 함수 호출하기

```kotlin
fun <T> List<T>.filter(predicate: (T) -> Boolean): List<T>

val authors = listOf("Dmitry", "Svetlana")
val readers = mutableListOf<String>(/*...*/)

readers.filter { it !in authors }
```

- 람다 파라미터에서 자동으로 만들어진 변수 it 타입은 T라는 제네릭 타입이다.
- `filter`의 수신 객체인 `reader`의 타입이 `List<String>`이라는 사실을 알고 `T`가 `String`이라는 사실을 추론한다.
- 제네릭 함수를 정의할 때와 마찬가지 방법으로 제네릭 확장 프로퍼티를 선언할 수 있다.
    
    ```kotlin
    // 모든 리스트 타입에 이 제네릭 확장 프로퍼티를 사용할 수 있다.
    val <T> List<T>.penultimate: T
    	get() = this[size - 2]
    
    // T는 Int로 추론된다.
    println(listOf(1,2,3,4).penultimate) // 3
    ```
    

<aside>
💡 확장 프로퍼티만 제네릭하게 만들 수 있다.
일반 (확장이 아닌) 프로퍼티는 타입 파라미터를 가질 수 없다. 클래스 프로퍼티에 여러 타입의 값을 저장할 수 없으므로 제네릭한 일반 프로퍼티는 말이 되지 않는다. 일반 프로퍼티를 제네릭하게 정의하면 컴파일러가 오류를 발생시킨다.

</aside>

## 제네릭 클래스 선언

- 코틀린도 타입 파라미터를 넣은 꺽쇠 기호(<>)를 클래스(또는 인터페이스) 이름 뒤에 붙이면 클래스(인터페이스)를 제네릭하게 만들 수 있다.
- 타입 파라미터를 이름 뒤에 붙이고 클래스 본문 안에서 타입 파라미터를 다른 일반 타입처럼 사용할 수 있다.

```kotlin
// List 인터페이스에 T라는 타입 파라미터를 정의한다.
interface List<T> {
	// 인터페이스 안에서 T를 일반 타입처럼 사용할 수 있다.
	operator fun get(index: Int): T
}
```

- 제네릭 클래스를 확장하는 클래스(또는 제네릭 인터페이스를 구현하는 클래스)를 정의하려면 기반 타입의 제네릭 파라미터에 대해 타입 인자를 지정해야 한다.
- 이때 구체적인 타입을 넘길 수도 있고 (하위 클래스도 제네릭 클래스라면) 타입 파라미터로 받은 타입을 넘길 수 있다.

```kotlin
// 이 클래스는 구체적인 타입 인자를 String을 지정해 List를 구현한다.
class StringList: List<String> {
	// String을 어떻게 사용하는지 살펴보라
	override fun get(index: Int): String = ...
}

// ArrayList의 제네릭 타입 파라미터 T를 List의 타입 인자로 넘긴다.
class ArrayList<T>: List<T> {
	override fun get(index: Int): T = ...
}
```

## 타입 파라미터 제약

- 타입 파라미터 제약(type parameter constraint)은 클래스나 함수에 사용할 수 있는 타입 인자를 제한하는 기능이다.
- 어떤 타입을 제네릭 타입의 타입 파라미터에 대한 상한(upper bound)로 지정하면 그 제네릭 타입을 인스턴스화할 때 사용하는 타입 인자는 반드시 그 상한 타입이거나 그 상한 타입의 하위 타입이어야 한다.
- 제약을 가하려면 타입 파라미터 이름 뒤에 콜론(:)을 표시하고 그 뒤에 상한 타입을 적으면 된다.

```java
<T extends Number> T sum <List<T> list)

```

![타입 파라미터 뒤에 상한을 지정함으로써 제약을 정의할 수 있다.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/312a8003-632a-4f03-893a-9b848e197b68/Untitled.png)

타입 파라미터 뒤에 상한을 지정함으로써 제약을 정의할 수 있다.

- 타입 파라미터 T에 대한 상한을 정하고 나면 T 타입의 값을 그 상한 타입의 값으로 취급할 수 있다.

```kotlin
// Number를 타입 파라미터 상한으로 정한다.
fun <T : Number> oneHalf(value: T): Double {
	// Number 클래스에 정의된 메서드를 호출한다.
	return value.toDouble() / 2.0
}
```

```kotlin
// 이 함수의 인자들은 비교 가능해야 한다.
fun <T: Comparable<T>> max(first: T, second: T): T {
	return if (first > second) first else second
}

// 문자열은 알파벳순으로 비교된다.
println(max("kotlin", "java")
```

- `T`의 상한 타입은 `Comparable<T>`다.
- 타입 파라미터에 대해 둘 이상의 제약을 가해야 하는 경우

```kotlin
fun <T> ensureTrailingPeriod(seq: T) 
	// 타입 파라미터 제약 목록이다.
	where T : CharSequence, T: Appendable {
		// CharSequence 인터페이스의 확장 함수를 호출한다.
		if(!seq.endsWith('.')) {
			// Appendable 인터페이스의 메서드를 호출한다.
			seq.append('.')
		}
	}
}
```

- 위의 코드는 타입 인자가 CharSequence와 Appendable 인터페이스를 반드시 구현해야한다는 사실을 표현해야 한다.
- 데이터에 접근하는 연산(endsWith)과 데이터를 변환하는 연산(append)을 T 타입의 값에게 수행할 수 있다는 뜻이다.

## 타입 파라미터를 널이 될 수 없는 타입으로 한정

- 제네릭 클래스나 함수를 정의하고 그 타입을 인스턴스화할 때는 널이 될 수 있는 타입을 포함하는 어떤 타입으로 타입인자를 지정해도 타입 파라미터를 치환할 수 있다.
- 아무런 상한을 정하지 않은 타입 파라미터는 결과적으로 Any?를 상한으로 정한 파라미터와 같다.

```kotlin
class Processor<T> {
	fun process(value: T) {
		// value는 널이 될 수 있다. 다라서 안전한 호출을 사용해야 한다.
		value?.hashCode()
	}
}
```

- process 함수에서 value 파라미터의 타입 T에는 물음표(?)가 붙어있지 않지만 실제로는 T에 해당하는 타입 인자로 널이 될 수 있는 타입을 넘길 수도 있다. T는 Any?를 상한으로 사용하고 있는 것과 다르지 않기 때문이다.

```kotlin
// 널이 될 수 있는 타입인 String?이 T를 대신한다.
val nullableStringProcessor = Processor<String?>()
// 이 코드는 잘 컴파일되며 'null'이 'value' 인자로 지정된다.
nullableStringProcessor.process(null)
```

- 항상 널이 될 수 없는 타입만 타입 인자로 받게 만들려면 타입 파라미터에 제약을 가해야 한다.
- 널 가능성을 제외한 아무런 제약도 필요 없다면 Any? 대신 Any를 상한으로 사용한다.

```kotlin
// null이 될 수 없는 타입 상한을 지정한다.
class Processor<T : Any> {
	fun process(value: T) {
		// T 타입의 value는 null이 될 수 없다.
		value.hashCode()
	}
}
```

- `<T: Any>`라는 제약은 T 타입이 항상 널이 될 수 없는 타입이 되게 보장한다.
- 컴파일러는 타입 인자인 `String?`가 `Any` 자손 타입이 아니므로 `Processor<String?>` 같은 코드를 거부한다.

```kotlin
val nullableStringProcessor = Processor<String?>()
// Error: Type argument is not within its bounds: should be subtype of 'Any'
```

- 타입 파라미터를 널이 될 수 없는 타입으로 제약하기만 하면 타입 인자로 널이 될 수 있는 타입이 들어오는 일을 막을 수 있다. 따라서 Any가 아닌 다른 타입으로 non-null 타입을 사용해 상한을 정해도 된다.

# 2. 실행 시 제네릭스의 동작: 소거된 타입 파라미터와 실체화된 타입 파라미터

---

- JVM의 제네릭스는 보통 타입 소거(type erasure)를 사용해 구현된다.
    - 실행 시점에 제네릭 클래스의 인스턴스에 타입 인자 정보가 들어있지 않다는 뜻이다.
- 함수를 inline으로 선언함으로써 이런 제약을 어떻게 우회할 수 있는지 살펴본다.
- 함수를 inline으로 만들면 타입 인자가 지워지지 않게 할 수 있다. 코틀린에서는 이를 실체화(reify)라고 한다.

## 실행 시점의 제네릭: 타입 검사와 캐스트

- 코틀린은 자바와 마찬가지로 런타임에 제네릭 타입 인자 정보가 지워진다.
    - 제네릭 클래스 인스턴스가 그 인스턴스를 생성할 때 쓰인 타입 인자에 대한 정보를 유지하지 않는다.

```kotlin
val list1: List<String> = listOf("a", "b")
val list2: List<Int> = listOf(1, 2, 3)
```

- 실행시점에 list1이나 list2가 문자열이나 정수의 리스트로 선언됐다는 사실을 알 수 없다. 각 객체는 단지 List일 뿐이다.
- 컴파일러는 둘을 서로 다른 타입으로 인식하지만 실행 시점에 그 둘은 완전히 같은 타입의 객체다.
- 컴파일러는 타입 인자를 알고 올바른 타입의 값만 각 리스트에 넣도록 보장한다. 자바 raw 타입을 사용해 리스트에 접근하고 타입 캐스트를 활용하면 컴파일러를 속일 수는 있지만 품이 많이 든다.

타입 소거로 인해 생기는 한계

- 타입 인자를 따로 저장하지 않기 때문에 실행시점에 타입 인자를 검사할 수 없다.

```kotlin
if (value is List<String>) ( ... )
// Error: Cannot check for instance of srased type
```

- 실행 시점에 어떤 값이 List인지 여부인지 알 수 있지만 그 리스트가 어느 타입의 element를 갖고 있는지 알 수 없다. 그 정보는 지워진다.
    - 다만 저장해야 하는 타입 정보의 크기가 줄어들어서 전반적인 메모리 사용량이 줄어든다는 장점이 있다.
- 코틀린에서 타입 인자를 명시하지 않고 제네릭 타입을 사용할 수 없다. 어떤 값이 집합이나 맵이 아니라 리스트라는 사실을 어떻게 확인할까? **스타 프로젝션(star projection)**을 사용하면 된다.

```kotlin
if (value is List<*>) { ... }
```

- 타입 파라미터가 2개 이상이라면 모든 타입 파라미터에 *를 포함시켜야 한다.
- 인자를 알 수 없는 제네릭 타입을 표현할 때(자바의 List<?>와 비슷하다) 스타 프로젝션을 쓴다.
- `as`나 `as?` 캐스팅에도 여전히 제네릭 타입을 사용할 수 있다. 기저 클래스는 같지만 타입 인자가 다른 타입으로 캐스팅해도 여전히 캐스팅에 성공한다. 실행 시점에는 제네릭 타입의 타입 인자를 알 수 없으므로 캐스팅은 항상 성공 한다. (컴파일러가 uncheckd cast(검사할 수 없는 캐스팅)이라고 경고를 준다.)

```kotlin
fun printSum(c: Collection<*>) {
	// 여기서 Unchecked cast: List<*> to List<Int> 경고 발생
	val intList = c as? List<Int>
		?: throw IllegalArgumentException("List is expected")
	println(intList.sum())
}
```

- 컴파일러가 캐스팅 관련 경고를 한다는 점을 제외하면 모든 코드가 문제없이 컴파일된다.
- 코틀린 컴파일러는 컴파일 시점에 타입 정보가 주어진 경우에는 `is` 검사를 수행하게 허용할 수 있을 정도로 똑똑하다.

```kotlin
fun printSum(c: Collection<Int>) {
	// 이 검사는 올바르다.
	if (c is List<Int>) {
		println(c.sum())
	}
}
```

- 코틀린은 제네릭 함수의 본문에서 그 함수의 타입 인자를 가리킬 수 있는 특별한 기능을 제공하지 않는다.
- inline 함수 안에서는 타입인자를 사용할 수 있다.

## 실체화한 타입 파라미터를 사용한 함수 선언

- 제네릭 클래스의 인스턴스가 있어도 그 인스턴스를 만들 때 사용한 타입 인자를 알아낼 수 없다. 제네릭 함수의 타입 인자도 마찬가지다. 제네릭 함수가 호출되도 그 함수의 본문에서는 호출 시 쓰인 타입 인자를 알 수 없다.

```kotlin
fun <T> isA(value: Any) = value is T
// Error: Cannot check for instance of erased type: T
```

- 인라인 함수의 타입 파라미터는 실체화되므로 실행 시점에 인라인 함수의 타입 인자를 알 수 있다.
    - `inline` 키워드를 붙이면 컴파일러는 그 함수를 호출한 식을 모두 함수 본문으로 바꾼다.
- 함수가 람다를 인자로 사용하는 경우 그 함수를 인라인 함수로 만들면 코드로 함께 인라이닝되고 그에 따라 무명 클래스와 객체가 생성되지 않아서 성능이 더 좋아질 수 있다.

```kotlin
// 이제는 이 코드가 컴파일 된다.
inline fun <reified T> isA(value: Any) = value is T
println(isA<String>("abc")
// true
println(isA<String>(123))
// fale
```

- 실체화한 타입 파라미터를 활용하는 가장 간단한 예제는 표준 라이브러리 함수인 filetIsInstance다. 이 함수는 인자로 받은 컬렉션의 원소 중에서 타입 인자로 지정한 클래스의 인스턴스만을 모아서 만든 리스트를 반환한다.

```kotlin
val items = listOf("one", 2, "three")
println(items.filterIsInstance<String>())
// [one, three]
```

- filterIsInstance는 그 타입 인자를 사용해 리스트의 원소 중에 타입 인자와 타입이 일치하는 원소만을 추려낼 수 있다.

```kotlin
// reified 키워드는 이 타입 파라미터가 실행 시점에 지워지지 않음을 표시한다.
inline fun <reified T> Iterable<*>.filterIsInstance(): List<T> {
	val destination = mutableListOf<T>()
	for (element in this) {
		// 각 원소가 탕비 인자로 지정한 클래스의 인스턴스인지 검사할 수 있다.
		if (element in this) {
			destination.add(element)
		}
	}
	return destination
}
```

### 인라인 함수에서만 실체화된 타입 인자를 쓸 수 있는 이유

- 컴파일러는 인라인 함수의 본문을 구현한 바이트코드를 함수가 호출되는 모든 지점에 삽입한다.
- 컴파일러는 실체화된 타입 인자를 사용해 인라인 함수를 호출하는 각 부분의 정확한 타입인자를 알 수 있다.
- 컴파일러는 타입 인자로 쓰인 구체적인 클래스를 참조하는 바이트코드를 생성해 삽입할 수 있다.

```kotlin
for (element in this) {
	// 특정 클래스 참조
	if (element is String) {
		destination.add(element)
	}
}
```

- 타입 파라미터가 아니라 구체적인 타입을 사용하므로 바이트코드는 실행 시점에 벌어지는 타입 소거의 영향을 받지 않는다.
- 자바 코드에서는 reified 타입 파라미터를 사용하는 inline 함수를 호출할 수 없다.
- 자바에서는 코틀린 인라인 함수를 다른 보통 함수처럼 호출한다. 그런 경우 인라인 함수를 호출해도 실제 인라이닝이 되지 않는다. 실체화환 타입 파라미터가 있는 함수의 경우 타입 인자 값을 바이트코드에 넣기 위해 일반 함수보다 더 많은 작업이 필요하다. 따라서 실체화한 타입 파라미터가 있는 인라이닝 함수를 일반 함수처럼 자바에서 호출할 수 없다.

<aside>
💡 함수의 파라미터 중에 함수 타입인 파라미터가 있고 그 파라미터에 해당하는 인자(람다)를 함께 인라이닝함으로써 얻는 이익이 더 큰 경우에만 함수를 인라인 함수를 만들어야 한다. 하지만 이 경우에는 함수를 inline으로 만드는 이유가 성능 향상이 아니라 실체화한 타입 파라미터를 사용하기 위함이다. 성능을 좋게 하려면 인라인 함수의 크기를 계속 관찰해야 한다.

</aside>

## 실체화한 타입 파라미터로 클래스 참조 대신

- `java.class.Class` 타입 인자를 파라미터로 받는 API에 대한 코틀린 어댑터(adpater)를 구축하는 경우 실체화한 타입 파라미터를 자주 사용한다.
    - `java.class.Class` API의 예로 JDK의 `ServiceLoader`가 있다. 추상클래스나 인터페이스를 표현하는 `java.class.Class` 를 받아서 그 클래스나 인스턴스를 구현한 인스턴스를 반환한다.

```kotlin
val serviceImpl = ServiceLoader.load(Service::class.java)
```

- 이 경우 코틀린 클래스에 대응하는 java.lang.Class 참조를 얻는 방법이다.

```kotlin
val serviceImpl = loadService<Service>()
```

- 구체화한 타입 파라미터를 사용하면 위와 같이나타난다.

```kotlin
// 타입 파라미터를 reified로 표시한다.
inline fun <reified T> loadService() {
	// T::class로 타입 파라미터의 클래스를 가져온다.
	return ServiceLoader.load(T::class.java)
}
```

- 타입 파라미터로 지정된 클래스에 따른 `java.lang.Class`를 얻을 수 있고 그렇게 얻은 클래스 참조를 보통때와 마찬가지로 사용할 수 있다.

## 실체화한 타입 파라미터의 제약

- 실체화한 타입 파라미터는 유용한 도구지만 몇 가지 제약이 있다.

다음과 같은 경우에 실체화한 타입 파라미터를 사용할 수 있다.

- 타입 검사와 캐스팅(is, !is, as, as?)
- 코틀릭 리플렉션 API(::class)
- 코틀린 타입에 대응하는 java.lang.Class를 얻기(::class.java)
- 다른 함수를 호출할 때 타입 인자로 사용

다음과 같은 일을 할 수 없다.

- 타입 파라미터 클래스의 인스턴스 생성하기
- 타입 파라미터 클래스의 동반 객체 메서드 호출하기
- 실체화한 타입 파라미터를 요구하는 함수를 호출하면서 실체화하지 않은 타입 파라미터로 받은 타입을 타입 인자로 넘기기
- 클래스, 프로퍼티, 인라인 함수가 아닌 함수의 타입 파라미터를 reified로 지정하기

실체화한 타입 파라미터를 인라인 함수에만 사용할 수 있으므로 실체화한 타입 파라미터를 사용하는 함수는 자신에게 전달되는 모든 람다와 함께 인라이닝된다. 람다 내부에서 타입 파라미터를 사용하는 방식에 따라 람다를 인라이닝할 수 없는 경우가 생기기도 하고 성능 문제로 람다를 인라이닝안하고 싶을 수 있다.

# 3. 변성: 제네릭과 하위 타입

---

**변성(variance)** 개념은 `List<String>`와 `List<Any>`와 같이 기저 타입이 같고 타입 인자가 다른 여러 타입이 서로 어떤 관계가 있는지 설명하는 개념이다.

## 변성이 있는 이유: 인자를 함수에 넘기기

- List<Any> 타입의 파라미터를 받는 함수에 List<String>을 넘기면 안전할까?

```kotlin
fun printContens(list: List<Any>) {
	println(list.joinToString())
}
```

- 각 원소를 `Any`로 취급하며 모든 문자열은 `Any` 타입이기도 하므로 완전히 안전하다.

```kotlin
fun addAnswer(list: MutableList<Any>) {
	list.add(42)
}
```

```kotlin
val strings = mutableListOf("abc", "bac")
// 이 줄이 컴파일된다면
addAnswer(strings)
println(strings.maxBy { it.length })
// 실행 시점에 예외가 발생할 것이다.
ClassCastException: Integer cannot be cast toString
```

- 코틀린 컴파일러는 이런 함수 호출을 금지한다.

- 코틀린에서는 리스트의 변경 가능성에 따라 적절한 인터페이스를 선택하면 안전하지 못한 함수 호출을 막을 수 있다.
- 함수가 읽기 전용 리스트를 받는다면 더 구체적인 타입의 원소를 갖는 리스트를 그 함수에 넘길 수 있다.
