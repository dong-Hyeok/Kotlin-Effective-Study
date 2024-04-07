### ✅ 플랫폼 타입

- 코틀린은 자바 등 다른 프로그래밍 언어에서 넘어온 타입들을 특수하게 다룬다.
- 특수하게 다룬다? → 자바에서 온 타입을 플랫폼 타입으로 추론한다.
- 플랫폼 타입은 null이 될 수도 있고 아닐 수도 있다.

<br><br>

### ✅ 플랫폼 타입을 지양 해야하는 이유

```kotlin
public class JavaClass {
    public String getString() {
        return null;
    }
}
```

- StateType

값을 가져오는 위치에서 NPE발생

```kotlin
fun stateType() {
    val javaString: String = JavaClass().value()
    println(javaString.length)
}
```

- PlatformType

값을 사용하는 위치에서 NPE발생

```kotlin
fun platformType() {
    val javaString = JavaClass().value() 
    println(javaString.length)
}
```


<br><br>

### ✅ 정리

위 예시를 살펴보면 플랫폼 타입은 타입 검사에서 문제가 검출되지 않는다. 
이는 추후 다른 사람이 해당 객체가 전파되는 상황(다른 곳에서 해당 객체를 사용하는 상황)에서 
타입 에러인지 또는 어디서 무엇때문에 발생하는 에러인지 검출되기 더욱 어려운 상황을 초래한다. 
때문에 플랫폼 타입 사용은 지양하는 것이 좋다.