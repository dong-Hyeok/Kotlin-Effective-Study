### ✅ 상태를 정의할 때는 변수와 프로퍼티의 스코프를 최소화하는 것이 좋다.

- 프로퍼티는 지역 변수를 사용하는 것이 좋다.
- 최대한 좁근 스포크를 갖게 변수를 사용하자.
- 람다에서 변수를 캡처한다.

> 코틀린의 스코프는 기존적으로 중괄호로 만들어지며, 내부 스코프에서 외부 스코프로만 접근이 가능하다.
>


```kotlin
// 잘못된 최적화
val primes: Sequence<Int> = sequence {
    var numbers = generateSequence(2) { it + 1 }

    var prime: Int
    while (true) {
        prime = numbers.first()
        println(prime)
        yield(prime)
        numbers = numbers.drop(1).filter { it % prime != 0 }
    }
}

// 정상 
val primes2: Sequence<Int> = sequence {
    var numbers = generateSequence(2) { it + 1 }

    while (true) {
        val prime = numbers.first()
        println(prime)
        yield(prime)
        numbers = numbers.drop(1).filter { it % prime != 0 }
    }
}

fun main() {
    print(primes.take(10).toList()) // [2,3,5,6,7,8,9,10,11,12]
    print(primes2.take(10).toList()) // [2,3,5,7,11,13,17,19,23,29]
}
```