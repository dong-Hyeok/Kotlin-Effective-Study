# 적절하게 null을 처리하라

> **null**
프로퍼티가 null이라는 것은 값이 설정되지 않았거나, 제거되었다는 것을 나타낸다.
> 

### ✅ nullable 타입 처리 방법 3가지

null은 최대한 명확한 의미를 갖는 것이 좋다. 이는 nullable 값을 처리해야 하기 때문이다. 

- `?.` , 스마트 캐스팅, Elvis 연산자 등을 활용해서 안전하게 저리
- 오류를 throw
- 함수 또는 프로퍼리를 리팩터링해서 nullable타입이 나오지 않게 바꾼다.

<br><br>

### ✅ `?.` , 스마트 캐스팅, Elvis 연산자 등을 활용해서 안전하게 저리

- 스마트 캐스팅

```kotlin
printer?.print() // safe call
if (printer != null) printer.print() // 스마트 캐스팅
```

- Elvis 연산자

```kotlin
val printerName1 = printer?.name ?: "Unnamed"
val printerName2 = printer?.name ?: return
val printerName3 = printer?.name ?: throw Error("Printer must be named")
```

- 코틀린 규약 기능

```kotlin
println("What is your name?")
val name = readLine()
if (!name.isNullOrBlank()) {
    println("Hello ${name.toUpperCase()}")
}

val news: List<News>? = getNews()
if (!news.isNullOrEmpty()) {
    news.foreach { notifyUser(it) } // if문 null 체크를 통한 not null로 스마트 캐스팅된 news 컬렉션
}
```

<br><br>

### ✅ 오류를 throw 하기

이전 printer 예제에서 printer가 null이 되리라 예상하지 못했다면, print 메소드가 호출되지 않아서 이상할 것이다.
이는 개발자가 오류를 찾기 어렵게 만든다.
그러한 부분에서 오류를 강제로 발생시켜 개발자에게 알려주는 것이 좋다.
오류를 강제로 발생시킬 때는 throw, !!, requireNotNull, checkNotNull 등을 활용한다.

```kotlin
fun process(user: User) {
    requireNotNull(user.name)
    val context = checkNotNull(context)
    val networkService = getNetworkService(context) ?: throw NoInternetConnection()
    networkService.getData { data, userData ->
        showFor(data!!, userData!!)
    }
}
```

<br><br>

### ✅ not-null assertion(!!)과 관련된 문제

nullable을 처리하는 가장 간단한 방법은 not-null assertion(!!)을 사용하는 것이다. 
그러나 !!를 사용하면 자바에서 nullable을 처리할 때 발생할 수 있는 똑같은 문제가 발생한다. 
어떤 대상이 null이 아니라고 생각하고 다루면, NPE가 발생하기 때문이다. 
!! 는 타입이 nullable이지만, null이 나오지 않는다는 것이 확실한 상황에서 많이 사용된다. 

일반적으로 !! 연산자 사용을 피하는 것을 권장한다. !! 연산자를 아예 사용하지 못하게 하는 정책도 있다. 
!! 연산자를 사용하면 미래에 코드가 변화하는 과정에서 어느 시점에 오류가 발생할 가능성을 염두해 둬야 한다. 

<br><br>

### ✅ 의미 없는 nullability 피하기

nullability는 어떻게든 적절하게 처리해야 하므로, 추가 비용이 발생한다.
따라서 필요한 경우가 아니라면, nullability 자체를 피하는 것이 좋다. 

- nullability를 피하는 방법
    1. 클래스에서 nullability에 따라 여러 함수를 만들어서 제공할 수도 있다. 
    ex) List<T>의 get, getOrNull 함수가 있다. 
    2. 어떤 값이 클래스 생성 이후에 확실하게 설정된다는 보장이 있다면, [lateinit 프로퍼티와 notNull 델리게이트](https://codechacha.com/ko/diff-between-deligate-and-lateinit-in-kotlin/#delegatesnotnull-%EC%86%8C%EA%B0%9C)를 사용하면 된다. 
    3. 빈 컬렉션 대신 null을 리턴하지 마라
    4. nullable enum과 none enum 값은 완전히 다른 의미이다.
    null enum은 별도로 처리해야 하지만, none enum 정의에 없으므로 필요한 경우에 사용하는 쪽에서 추가해서 활용할 수 있다. 
        
        

### **Nullable Enum 처리**

`State` enum 타입을 nullable로 처리하는 경우입니다. 이 경우, enum 값이 `null`일 수 있으므로, `null`을 별도로 체크하고 처리해야 한다.

```kotlin
enum class State {
    ACTIVE, INACTIVE, PENDING
}

fun processStateNullable(state: State?) {
    when (state) {
        State.ACTIVE -> println("활성 상태")
        State.INACTIVE -> println("비활성 상태")
        State.PENDING -> println("대기 중")
        null -> println("상태 정보 없음")
    }
}

// 사용 예
processStateNullable(State.ACTIVE)
processStateNullable(null)
```

### **None Enum 처리**

이번에는 `State` enum에 `None` 값을 추가하여, '값이 없음'을 명시적으로 표현하는 방법이다. 
이 경우, 모든 상태는 `State` enum의 인스턴스이며, `null`을 별도로 처리할 필요가 없다.

```kotlin
kotlinenum class StateWithNone {
    ACTIVE, INACTIVE, PENDING, NONE// 'None' 값 추가
}

fun processStateWithNone(state: StateWithNone) {
    when (state) {
        StateWithNone.ACTIVE -> println("활성 상태")
        StateWithNone.INACTIVE -> println("비활성 상태")
        StateWithNone.PENDING -> println("대기 중")
        StateWithNone.NONE -> println("상태 정보 없음")// 명시적으로 'None' 처리
    }
}

// 사용 예
processStateWithNone(StateWithNone.ACTIVE)
processStateWithNone(StateWithNone.NONE)

```

<br><br>

### ✅ lateinit 프로퍼티와 notNull 델리게이트

클래스가 클래스 생성 중에 초기화할 수 없는 프로퍼티를 가질 수 있으며, 이러한 프로퍼티는 사용 전에 반드시 초기화해서 사용해야 한다.

이러한 코드에 대한 바람직한 해결책은 나중에 속성을 사용하기 전에 반드시 초기화될 거라고 예상되는 상황에서 lateinit 한정자를 사용하는 것이다.

초기화 전에 값을 사용하려고 하면 예외가 발생하며, 처음 사용하기 전에 반드시 초기화될 거라고 예상되는 경우에만 사용해야 한다.

lateinit은 nullable과 비교해서 다음과 같은 차이가 있다.

- !! 연산자로 언팩(unpack)하지 않아도 된다.
- 이후에 어떤 의미를 나타내기 위해 null을 사용하고 싶을 때, nullable로 만들 수 있다.
- 프로퍼티가 초기화된 이후에는 초기화되지 않은 상태로 돌아갈 수 없다.

lateinit을 사용할 수 없는 경우도 있다.

기본(Primitive) 타입은 lateinit으로 선언할 수 없으며,

Int, Long, Double, Boolean과 같은 기본 타입과 연결된 타입으로 프로퍼티를 초기화해야 하는 경우는 lateinit을 사용할 수 없습니다.

이런 경우에는 Delegates, notNull을 사용하며, 프로퍼티 위임(property delegation)을 사용할 수도 있습니다.

```kotlin
class Rectangle {
    lateinit var area: Area
    fun initArea(param: Area): Unit {
        this.area = param
    }
}
```

```kotlin
var nonNullString: String by Delegates.notNull<String>()
nonNullString = "Hello World"
println("Non null value is: ${nonNullString}")
nonNullString = null  // 컴파일 에러, non-null 타입에 null을 넣을 수 없음
```

[Kotlin - lateinit과 Delegates.notNull의 차이점](https://codechacha.com/ko/diff-between-deligate-and-lateinit-in-kotlin/#delegatesnotnull-소개)