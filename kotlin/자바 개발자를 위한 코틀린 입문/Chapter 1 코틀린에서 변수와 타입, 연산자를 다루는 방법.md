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
* 플랫폼 타입
  * Kotlin 에서 Java 코드를 가져다 사용할 때 어떻게 처리하는가?
  * 