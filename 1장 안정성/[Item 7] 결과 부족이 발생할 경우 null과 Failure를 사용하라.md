# 결과 부족이 발생할 경우 null과 Failure를 사용하라

### ✅ 함수가 원하는 결과를 만들어 낼 수 없는 상황 & 처리 메커니즘

- 함수가 결과를 만들어 낼 수 없는 상황
    - 서버로 부터 데이터를 읽어 들이는 과정에서 인터넷 연결 중단
    - 조건에 맞는 요소 탐색 실패
    - 텍스트 파싱시 타입 불일치

- 처리 메커니즘
    - null 또는 실패를 나타나는 sealed 클래스를 리턴
    - 예외 throw

- 예외는 예외적인 상황이 발생했을 때 사용하는 것이 좋은 이유
    - 예외는 정보 전달하는 방법으로 사용되서는 안된다. 예외는 특별한 상황을 나타내고 처리되어야 한다.
    - 많은 개발자가 예외가 전파되는 과정을 제대로 추적하지 못한다.
    - 코틀린의 모든 예외는 unchecked 예외이다. 따라서 사용자가 예외를 처리하지 않을 수도 있으며, 이와 관련된 내용은 문서에 제대로 드러지 않는다.
    - 예외는 예외적인 상황을 처리하기 위해 만들어졌으므로 명시적인 테스트 만큼 빠르게 동작하지 않는다.
    - try-catch 블록 내부에 코드를 배치하면, 컴파일러가 할 수 있는 최적화가 제한된다.
    
    > checked: 사용자가 반드시 처리하게 강제되는 예외
    unChecked: 처리하지 않아도 실행에 문제가 없는 예외
    > 

- null 또는 실패를 나타나는 sealed 클래스를 사용하면 좋은 이유
    - 오류를 표현할 때 명시적이고, 효율적이며 간단하게 처리할 수 있다.

> **정리**
> 
> - 충분히 예측할 수 있는 범위의 오류는 null과 Failure를 사용
> - 예측하기 어려운 예외적인 범위의 오류는 예외를 throw

<br><br>

### ✅ 예시

```kotlin
inline fun <reified T> String.readObjectOrNull(): T? {
    //...
    if (incorrectSign) {
        return null
    }
    //...
    return result
}

inline fun <reified T> String.readObject(): Result<T> {
    //...
    if (incorrectSign) {
        return Failure(JsonParsingException())
    }
    //...
    return Success(result)
}

sealed class Result<out T>
class Success<out T>(val result: T): Result<T>()
class Failure(val throwable: Throwable): Result<Nothing>()

class JsonParsingException: Exception()
```

- Elvis연산자를 사용한 null 체크

```kotlin
val age = userText.readObjectOrNull<Person>()?.age ?: -1
```

- when 표현식을 사용한 null 체크

```kotlin
val person = userText.readObjectOrNull<Person>()
val age = when(person) {
    is Success -> person.age
    is Failure -> -1
}
```

```kotlin
data class Person (
    val name: String,
    val age: Int
)

fun main() {
    val userText = "테스트!"
    val person = userText.readObject<Person>()
    val age = when(person) {
        is Success -> person.result.age
        is Failure -> -1
    }
}
```

<br><br>

### ✅ null 값과 Sealed result 클래스 차이점

- 추가적인 정보를 전달해야 한다면 sealed result 사용
- 그렇지 않으면 null을 사용