# 2. 함수, 변수, 클래스, enum, 프로퍼티를 선언하는 방법

### 2.1. 함수

- `fun` 키워드를 사용해서 함수를 선언
- 함수의 반환 타입은 함수 파라메터 다음에 오며 생략할 수 있음

```kotlin
fun max(a: Int, b: Int): Int {
		return if (a > b) a else b
}

fun max(a: Int, b: Int) {
		return if (a > b) a else b
}
```

### 2.2. 문(statement) vs 식(expression)

- 코틀린 if는 식이다. → 값을 만들어 낼 수 있다

```kotlin
it("식(expression)은 값을 만들어낸다") {
    val a = 10
    val b = 20
    val expression = if (a > b) a else b

    expression shouldBe b
}
```

### 2.3. 식이 본문인 함수

```kotlin
fun max(a: Int, b: Int) = if (a > b) a else b
```

- 컴파일러 타입추론을 통해 반환타입 생략 가능
- 함수의 동작이 비교적 간단하고 직관적일때만 `식이 본문인 함수` 를 사용하는 것이 좋아보임

### 함수 아젠다

- 식이 본문인 함수 vs 블록이 본문인 함수
- 리턴타입 명시 vs 생략

# 2.2 클래스와 프로퍼티

```kotlin
class Person(val name: String)
```

- `public` 가시성 변경자가 생략 → default

### 2.2.1 프로퍼티

- 클래스: 데이터를 캡슐화하고 캡슐화한 데이터를 다루는 코드를 한 곳에서 관리
- 프로퍼티: 필드 + 접근자(게터 & 세터)
- 코틀린은 프로퍼티를 언어레벨에서 제공

```kotlin
class Person(
		val name: String, // 읽기 전용 프로퍼티 -> 게터를 제공
		var isMarried: Boolean, // 쓸수 있는 프로퍼티 -> 게터와 세터를 제공
)
```

### 2.2.2 커스텀접근자

```kotlin
class Rectangle(val height: Int, vla width: Int){
		val isSquare: Boolean
		get() {
				return height == width
		}
		
		// get() = height == width
}
```

### 2.2.3 아젠다

- 접근자 메서드를 사용하는것 vs 필드를 public으로 열어서 사용하는것 차이는?

# enum과 when

- enum 클래스 안에도 프로퍼티와 메서드를 정의할 수 있다.

```kotlin
enum class Color(
		val r: Int, val g: Int, b: Int
) {
		// 각 상수를 생성할 때 프로퍼티의 값을 지정
		RED(255, 0, 0), ORANGE(255, 165, 0); // enum 클래스 안에서 함수를 지정하는 경우 세미콜린 사용
		
		fun rgb() = (r * 256 + g) * 256 + b
}
```

- 적용예제

```kotlin
enum class OperatorType(val symbol: String) {
    ADD(symbol = "+"), 
    SUBTRACT(symbol = "-"), 
    MULTIPLY(symbol = "*"),
    DIVIDE(symbol = "-"),
}

OperatorType.ADD.symbol
```

- 자바와 달리 when의 분기조건에서 임의의 객체 허용

```kotlin
fun mix(c1: Color, c2:Color) = when(setOf(c1, c2)) {
		setOf(RED, YELLOW) -> ORANGE,
		setOf(YELLOW, BLUE) -> GREEN
}
```

- setOf()로 분기조건을 검사할 때 동등성으로 검사
- 분기를 검사할 때마다 Set 객체 생성 → 가비지 증가

- 인자가 없는 when 절

```kotlin
fun mixOptimized(c1: Color, c2: Color) = 
			when {
					(c1 == RED && c2 == YELLOW) ||
					(c1 == YELLOW && c2 == RED0 -> ORANGE
					
					// 생략
			}
```

- 가독성 vs 성능

### 2.2.5 스마트캐스트

- 코틀린에서 컴파일러가 특정 변수의 타입을 자동으로 판단하여 명시적인 캐스팅 없이도 안전하게 타입을 변환해 사용하는 기능 → instanceof 없이 타입을 변환
- if, when 등에서 타입이 특정 조건에 의해 확인되면, 명시적으로 타입을 캐스팅 하지 않아도 해당 타입으로 사용이 가능하게 변경

```kotlin
fun smartCastExample(obj: Any): Int {
    return if (obj is String) {
        obj.length // 스마트캐스트로 String 타입으로 간주되어 length 사용 가능
    } else if (obj is Int) {
        obj * 2 // 스마트캐스트로 Int 타입으로 간주되어 연산 가능
    } else {
        -1
    }
}
```

```kotlin
open class Animal

class Dog : Animal() {
    fun bark() = "멍!"
}

fun smartCastWithClass(animal: Animal) {
    if (animal is Dog) {
        println(animal.bark()) // 스마트캐스트로 Dog 타입으로 간주
    } else {
        println("강아지 아님")
    }
}

```

- 스마트캐스트 동작 조건
    1. 변수는 불변(val)으로 선언되어야 함.
    2. 스마트캐스트는 불변 지역 변수 또는 로컬 변수에서만 동작.
    3. 타입 체크는 런타임에 `is` 키워드를 통해 이루어짐.

# 이터레이션

### 숫자 이터레이션

- for 루프

```kotlin
for (i in 1..5) { // 1부터 5까지 포함
    println(i) // 1, 2, 3, 4, 5 출력
}
```

- 역방향 반복

```kotlin
for (i in 5 downTo 1) { // 5부터 1까지 포함
    println(i) // 5, 4, 3, 2, 1 출력
}
```

- step

```kotlin
for (i in 1..10 step 2) { // 2씩 증가
    println(i) // 1, 3, 5, 7, 9 출력
}
```

- 주의: `step 2` 같은 매직넘버를 의미있는 상수로 추출

- until

```kotlin
for (i in 1 until 5) { // 1부터 4까지 (5 제외)
    println(i) // 1, 2, 3, 4 출력
}
```

### in

- `in`은 범위 안에 값이 포함되어 있는지 확인

```kotlin
val x = 5
if (x in 1..10) { // x가 1부터 10 사이에 있는지 확인
    println("$x is in range") // 출력: 5 is in range
}
```

- `!in`을 사용하면 범위에 포함되지 않는 경우를 확인

```kotlin
val y = 15
if (y !in 1..10) { // y가 1부터 10 사이에 없는지 확인
    println("$y is not in range") // 출력: 15 is not in range
}
```

### 맵 이터레이션

- entry 반복

```kotlin
val map = mapOf("A" to 1, "B" to 2, "C" to 3)

for ((key, value) in map) {
    println("$key -> $value") // A -> 1, B -> 2, C -> 3 출력
}
```

- 맵의 `entry`를 사용하면 각 키와 값을 함께 처리

- key만 반복

```kotlin
for (key in map.keys) {
    println(key) // A, B, C 출력
}
```

- value만 반복

```kotlin
for (value in map.values) {
    println(value) // 1, 2, 3 출력
}
```

- 확장함수 forEach 사용

```kotlin
map.forEach { (key, value) ->
    println("$key -> $value")
}
```

# 코틀린 예외처리

- 문법위주
- thorw는 식 → 다른 식에 포함될 수 없다

```kotlin
val percentage = 
		if (number in 0 .. 100)
				number
		else 
				throw IllegalArgumentException("message")
```

- 조건이 참이면 변수가 초기화됨
- 조건이 거짓이면 초기화 되지 않음 → 6장

### 코틀린 예외 특징

- 체크예외와 언체크 예외를 구분하지 않는다 → 체크 예외를 처리하지 않아도 된다.
- 자바 개발자들이 예외를 제대로 처리하지 않거나 무시하는 경우가 많아 이를 고려했다.
- `try with resource`  기능을 제공하지 않음 → 라이브러리를 통해 같은 기능을 구현 (8장)
    - try-catch-resource
