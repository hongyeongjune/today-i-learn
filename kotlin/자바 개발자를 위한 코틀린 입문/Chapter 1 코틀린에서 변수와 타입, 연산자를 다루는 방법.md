### 코틀린에서 변수를 다루는 방법
* 모든 변수는 var / val 를 사용해야한다.
  * var = 변경 가능
  * val = 변경 불가능 (자바에서 final 키워드와 같은 효과)
* 타입을 명시적으로 작성하지 않아도, 타입이 추론된다.
* Primitive Type과 Reference Type을 구분하지 않아도 된다.
  * 코틀린은 내부적으로 필요에 따라 primitive type을 사용한다.
  * reference type은 박싱,언박싱 때문에 성능이 안좋아질 수 있는데, 코틀린이 알아서 잘 골라주기 때문에 성능을 걱정할 필요가 없다.
  * https://kotlinlang.org/docs/basic-types.html
* Null이 들어갈 수 있는 변수는 타입 뒤에 ? 를 붙여주어야 한다.
* 객체를 인스턴스화 할 때 new를 붙이지 않아야 한다.

### 코틀린에서 null을 다루는 방법
* kotlin 에서의 null 체크
  * 아래 코드의 예시처럼 null을 체크하는 다양한 방법이 있는데, "?" 연산을 사용하여 null이 들어가는지 아닌지 타입을 명시하여 체크할 수 있다.
```kotlin
fun startsWithA1(str: String?): Boolean { 
  if (str == null) { 
      throw new IllegalArgumentException("null") 
  }
  return str.startsWith("A") 
}

fun startsWithA2(str: String?): Boolean? {
  if (str == null) {
    return null
  }
  return str.startsWith("A")
}

fun startsWithA3(str: String?): Boolean {
  if (str == null) {
    return false
  }
  return str.startsWith("A")
}
```
* Safe Call
  * null 이 아니면 실행하고, null 이면 실행하지 않는다. (그대로 null)
  * ?.
```kotlin
val str: String? = "ABC"
str.length // 불가능
str?.length // 가능
```
* Elvis 연산자
  * 앞의 연산 결과가 null 이면 뒤의 값을 사용
  * ?:
```kotlin
val str: String? = "ABC"
str?.length ?: 0 //앞의 결과가 null 이면 0 출력
```
* Safe call + Elvis 연산을 합친 결과
```kotlin
fun startsWithA1(str: String?): Boolean { 
  return str?.startsWith("A") ?: throw new IllegalArgumentException("null")
}

fun startsWithA2(str: String?): Boolean {
  return str?.startsWith("A")
}

fun startsWithA1(str: String?): Boolean {
  return str?.startsWith("A") ?: false
}
```
* null 아님 선언
  * !!
```kotlin
fun startsWith(str: String?): Boolean {
    return str!!.startsWith("A")
}
```

### 코틀린에서 Type 을 다루는 방법
* 코틀린에서는 암시적 타입 변경이 불가능하다.
  * to변환타입()을 사용하면된다.
  * nullable 이라면 적절한 처리가 필요하다.
```kotlin
fun main() {
  val number1 = 3
  val number2: Long = number1.toLong()
  val number3 = 5
  val result = number1 / number2.toDouble()
  val number4: Int = 4
  val number5: Long = number4?.toLong() ?: 0L
}
```
* 타입 캐스팅 (일반 타입)
  * value is Type
    * value 가 Type 이면 true
    * value 가 Type 이 아니면 false
  * value !is Type
    * value 가 Type 이면 false
    * value 가 Type 이 아니면 true
  * value as Type
    * value 가 Type 이면 Type 으로 캐스팅
    * value 가 Type 이 아니면 예외 발생
  * value as? Type
    * value 가 Type 이면 Type 으로 캐스팅
    * value 가 Type 이 아니면 null
    * value 가 null 이면 null
```kotlin
fun printAgeIfPerson(obj: Any) { 
  if (obj is Person) { // 자바에서 obj instanceof Person 과 같다. 
    val person = obj as Person // 자바에서 (Person) obj 와 같다.
    println(person.age)
  }
  if (obj !is Person) { // 자바에서 !(obj instanceof Person) 과 같다.
    println("not Person")
  }
}
fun printAgeIfPerson(obj: Any?) {
  val person = obj as? Person // null 이라면 전체가 null 이 되고 아니면 person 으로 변경
  println(person?.age)
}
```
* Any 타입
  * Java 의 Object 역할 (모든 객체의 최상위 타입)
  * 모든 Primitive Type 의 최상위 타입도 Any
  * Any 자체로는 null 을 포함할 수 없어 null 을 포함하고 싶다면, Any?로 표현
  * Any 에 equals / hashCode / toString 존재
* Unit 타입
  * Unit 은 자바의 void 와 동일한 역할
* Nothing 타입
  * Nothing 은 함수가 정상적으로 끝나지 않았다는 사실을 표현하는 역할
  * 무조건 예외를 반환하는 함수 / 무한 루프 함수 등에서 사용
```kotlin
fun fail(message: String): Nothing {
    throw IllegalArgumentException(message)
}
```
* String interpolation / String indexing
  * 문자열 타입 사용
```kotlin
val person = Person("홍영준", 100)
// 자바에선 String.format() 을 사용했어야함
// 중괄호 생략도 가능
val log = "사람의 이름은 ${person.name}이고 나이는 ${person.age}세 입니다."
```
  * trimIndent (큰따옴표 세개) => """"""
```kotlin
val log = """
    ABC
    EFG
""".trimIndent()
// 결과물
// ABC
// EFG
```
  * 문자열의 특정 문자 가져오기
```kotlin
val str = "ABCDE"
val ch = str[1]
```

### 코틀린에서 연산자를 다루는 방법
* 단항 연산자 / 산술 연산자 / 산술대입 연산자 => 자바와 동일
  * 단항 연산자 : ```++```, ```--```
  * 산술 연산자 : ```+```, ```-```, ```*```, ```/```, ```%```
  * 산술대입 연산자 : ```+=```, ```-=``` ...
* 비교 연산자와 동등성, 동일성 => 자바와 동일 (단, 객체는 다름)
  * 비교 연산자 : ```>```, ```<```, ```>=```, ```<=```
  * 객체를 비교할 때 비교 연산자를 사용하면 **자동으로 compareTo를 호출**한다.
  * 동등성 : 두 객체의 값이 같은가?
    * ```equals()```
    * 코틀린에서는 ```==```을 사용한다. => 이렇게 사용하면 간접적으로 ```equals```를 호출해준다.
  * 동일성 : 완전히 동일한 객체인가? (객체의 reference 주소가 같은가?)
    * ```==```
    * 코틀린에서는 ```===``` 을 사용한다.
* 논리 연산자 => 자바와 동일
  * ```&&```, ```||```, ```!```
  * 자바처럼 Lazy 연산 수행
* 코틀린의 특이한 연산자
  * ```in```, ```!in```
    * 컬렉션이나 범위에 포함되어 있다. or 포함되어 있지 않다.
  * ```a..b```
    * a 부터 b 까지의 범위 객체를 생성한다. (나중에 자세히 다룰 예정)
  * ```a[i]```
    * a에서 특정 Index i로 값을 가져온다.
* 연산자 오버로딩
  * 객체마다 연산자를 직접 정의할 수 있다. (플러스 연산, 마이너스 연산 등...)