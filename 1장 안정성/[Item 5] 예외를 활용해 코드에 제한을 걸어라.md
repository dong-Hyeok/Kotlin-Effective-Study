확실한 형태로 동작해야 하는 코드가 있다면, 예외를 활용해 제한을 걸어두는 것이 좋다.

- **require** 블록: 아규먼트를 제한할 수 있다.
- **check** 블록: 상태와 관련된 동작을 제한할 수 있다.
- **assert** 블록: 어떤 것이 true인지 확인할 수 있다. assert 블록은 테스트 모드에서만 작동한다.
- **return** 또는 **throw**와 함께 활용하는 **Elvis** 연산자

<br><br>

### ✅ 아규먼트(argument)

함수를 정의할 때 타입 시스템을 활용해서 아규먼트(argument)에 제한을 거는 코드를 많이 사용한다. 
일반적으로 함수의 가장 앞부분에 하게 되므로, 코드를 읽을 때 쉽게 확인할 수 있다. 

### require()

- `require` 함수는 함수의 인자로 전달된 값이 특정 조건을 만족하는지 검사하는 데 사용됩니다. 즉, 함수에 전달된 입력 값의 유효성을 검증할 때 주로 사용 된다.

```kotlin
fun setPercentage(percentage: Int) {
    try {
        require(percentage in 0..100) { "Percentage must be between 0 and 100" }
        // 유효성 검사를 통과하면, 여기서 로직을 계속 구현합니다.
        println("The provided percentage is $percentage")
    } catch (e: IllegalArgumentException) {
        // IllegalArgumentException이 발생했을 때의 처리 로직
        println("Error: ${e.message}")
    }
}

fun main() {
    setPercentage(101)  // 유효하지 않은 값으로 함수를 호출합니다.
    setPercentage(50)   // 유효한 값으로 함수를 호출합니다.
}
```

 `percentage`가 0과 100 사이에 있는지 검사한다. 만약 `percentage`가 이 범위를 벗어나면, `require` 함수는 `IllegalArgumentException`을 발생시키고, "Percentage must be between 0 and 100"라는 메시지를 예외에 포함시킨다.

<br><br>

### ✅ 상태

어떤 구체적인 조건을 만족할 때만 함수를 사용할 수 있게 해야 할 떄가 있다. 

- 어떤 객체가 미리 초기화되어 있어야만 처리를 하게 하고 싶은 함수
- 사용자가 로그인했을 때만 처리를 하게 하고 싶은 함수
- 객체를 사용할 수 있는 시점에 사용하고 싶은 함수

### check()

- `check` 함수는 프로그램의 상태나 객체의 상태가 특정 조건을 만족하는지 검사하는 데 사용된다.

```kotlin
// 사용자의 로그인 상태를 반환하는 함수 (임의로 구현)
fun isUserLoggedIn(): Boolean {
    return false
}

fun performAction() {
    check(isUserLoggedIn()) { "User must be logged in to perform this action" }
    
    println("Performing action for the user")
}

fun main() {
    try {
        performAction() // 이 함수를 호출하면, isUserLoggedIn이 false를 반환하기 때문에 IllegalStateException이 발생합니다.
    } catch (e: IllegalStateException) {
        println("Error: ${e.message}") // 에러 메시지를 출력합니다.
    }
}

```

`check` 함수는 `require`와 비슷하지만, 지정된 예측을 만족하지 못할 때, `IllegalStateException`를 `throw`한다. 

> 함수 전체에 대한 어떤 예측이 있을 때는 일반적으로 require 블록 뒤에 check 블록을 배치한다. 
즉, check를 나중에 한다.
> 

<br><br>

### ✅ Assert 계열 함수 사용

Kotlin과 같은 프로그래밍 언어에서 `assert` 계열의 함수는 개발 과정에서 특정 조건이 참인지 검증하는 데 사용된다.
`assert` 함수를 사용하는 주된 목적은 개발 단계에서 버그를 조기에 발견하고 수정하는 것이다.

- Assert 계열의 함수는 코드를 자체 점검하며, 더 효율적으로 테스트할 수 있게 해준다.
- 특정 상황이 아닌 모든 상황에 대한 테스트를 할 수 있다.
- 실행 시점에 정확하게 어떻게 되는지 확인할 수 있다.
- 실제 코드가 더 빠른 시점에 실패하게 만든다. 따라서 예상하지 못한 동작이 언제 어디서 실행되었는지 쉽게 찾을 수 있다.
- `assert` 함수는 기본적으로 JVM의 `-ea` (enable assertions) 옵션이 활성화되어 있을 때만 실행된다. 
(해당 옵션이 없으면 `assert`는 무시된다.)

 `assert` 함수는 조건을 하나의 인자로 받으며, 이 조건이 `false`일 경우 `AssertionError`를 발생시킵니다

```kotlin
fun main() {
    val positiveNumber = -10

    // assert 함수를 사용하여 조건을 검사하고, 조건이 false일 경우 AssertionError를 발생시킵니다.
    assert(positiveNumber > 0) { "제공된 숫자는 양수여야 합니다. 실제 값: $positiveNumber" }

    println("프로그램이 성공적으로 실행되었습니다.")
}

```

> 표준 애플리케이션 실행에서는 assertrk 예외를 throw하지 않는다! 
`assert`는 주로 개발 단계에서 오류를 찾는 데 사용되며, 프로덕션 코드에서는 사용을 지양해야 한다. 
만약 해당 코드가 정말 심각한 오류라면 check를 사용하는 것이 좋다.
>
> 

<br><br>

### ✅ nullability와 스마트 캐스팅

require와 check 블록으로 어떤 조건을 확인해서 true가 나왔다면, 해당 조건은 이후로도 true일 거라고 가정하는 것을 스마트 캐스팅이라고 한다.
require와 check 둘 다 스마트 캐스트를 지원하므로, 변수를 언팩하는 용도로 활용할 수 있다.

```kotlin
fun changeDress(person: Person) {
    require(person.outfit is Dress)
    val dress: Dress = person.outfit
}

fun sendEmail(person: Person, message: String) {
    require(person.email != null)
    val email: String = person.email
}
```

- `checkNotNull`, `requireNotNull`

```kotlin
fun sendEmail(person: Person, message: String) {
    val email = requireNotNull(person.email)
    println(email)
}
```

- nullability를 목적으로 Elvis연산자와 throw 또는 return을 사용하기

```kotlin
fun getName(person: Person?): String {
    // person의 name 프로퍼티가 null일 경우 "Unknown"을 반환합니다.
    return person?.name ?: return "Unknown"
}

fun getAge(person: Person?): Int {
    // person의 age 프로퍼티가 null일 경우 IllegalArgumentException 예외를 던집니다.
    return person?.age ?: throw IllegalArgumentException("Age cannot be null")
}
```