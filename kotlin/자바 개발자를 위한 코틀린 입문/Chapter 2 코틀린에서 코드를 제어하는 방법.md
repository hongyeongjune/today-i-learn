### 코틀린에서 조건문을 다루는 방법
* if 문
  * 자바에서 if-else 는 Statement 지만, 코틀린에서는 Expression 이다.
    * Statement : 프로그램의 문장, 하나의 값으로 도출되지 않는다.
    * Expression : 하나의 값으로 도출되는 문장
  * 즉, 코틀린은 Expression 이 가능하기 때문에 삼항 연산자가 필요없고 쓸 수도 없다. (삭제됨)
  * if - else if - else 문법도 같다.
```kotlin
fun getPassOrFail(score: Int): String {
    return if (score >= 50) {
        "P"
    } else {
        "F"
    }
}

fun getGrade(score: Int): String {
    return if (score >= 90) {
        "A"
    } else if (score >= 80) {
        "B"
    } else if (score >= 70) {
        "C"
    } else {
        "D"
    }
}

// Tip 범위 연산 => 0 <= score && score <= 100
fun validateScore(score: Int) {
    if (score !in 0..100) {
        throw IllegalArgumentException("score 범위가 아님")
    }
}
```

* switch 와 when
  * 자바의 switch 를 변경하면 다음의 when 과 같다.
  * expression 으로 사용할 수 있다.
```kotlin
fun getGrade(score: Int): String {
    return when (score / 10) {
        9 -> "A"
        8 -> "B"
        7 -> "C"
        else -> "D"
    }
}

fun getGradeRange(score: Int): String {
    return when (score) {
        in 90..99 -> "A"
        in 80..89 -> "B"
        in 70..79 -> "C"
        else -> "D"
    }
}
```
* 구문으로 치면 다음과 같다.
```
when(값) {
    조건부 -> 어떠한 구문
    조건부 -> 어떠한 구문
    else -> 어떠한 구문
}
```
* 조건부에는 어떠한 expression 이라도 들어갈 수 있다. (ex. is Type)
```kotlin
fun startsWithA(obj: Any): Boolean {
    return when (obj) {
        is String -> obj.startsWith("A")
        else -> false
    }
}
```
* 여러개의 조건을 동시에 사용할 수 있다.
```kotlin
fun judgeNumber(number: Integer) {
    when (number) {
        1, 0, -1 -> println("1, 0, -1 중 하나 입니다.")
        else -> println("올바른 값이 아닙니다.")
    }
}

fun judgeNumber2(number: Integer) {
    when {
        number == 0 -> println("0")
        number % 2 -> println("짝수")
        else -> println("홀수")
    }
}
```