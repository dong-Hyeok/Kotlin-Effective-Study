### ✅ var 보다는 val을 사용하여 가변성을 줄이자!

- var
    - 읽고 쓰기가 모두 가능한 프로퍼티
- val
    - 읽기 전용 프로퍼티지만, 변경할 수 없음을 의미하는 것은 아니다.
    - 완전 불변이 필요하다면 final 프로퍼티를 사용하는 것이 좋다.
  
<br><br>

### ✅ **val & 스마트 캐스트**

- 타입 캐스트가 불가능한 경우
    
    ```kotlin
    val name: String? = "dong"
    val surname: String = "hyeok"
    
    val fullName: String?
        get( ) = name?.let{ "$it $surname"}
    
    fun main() {
    // 타입 캐스트가 불가능하기 때문에 fullName!!.length 와 같이 null이 아님을 강제해야 한다.if(fullName != null) {
            println(fullName!!.length)
        }
    }
    
    ```
    
- 타입 캐스트가 가능한 경우
    
    ```kotlin
    val name: String? = "dong"
    val surname: String = "hyeok"
    
    val fullName: String? = name?.let{ "$it $surname"}
    
    fun main() {
        if(fullName != null) {
            println(fullName.length)// 타입 캐스트가 불가능하기 때문에 fullName!!.length
        }
    }
    
    ```
    <br><br>

### ✅ **컬렉션**

- 읽기 전용
    - Iterable
    - Collection
    - Set
    - List
- 읽고 쓰기 가능
    - MutableInterable
    - MutableCollection
    - MutableSet
    - MutableList

> 읽기 전용 컬렉션을 Mutable 컬렉션으로 다운캐스팅 하면 안된다!
> 
> 
> 만약 읽기 전용 컬렉션을 Mutable로 변경해야 한다면, 복제를 통해 새로운 Mutable 객체에 Copy를 하면된다.
> 
> 이렇게 하면 기존 객체는 여전히 immutable하다.
>

<br><br>

### ✅ **immutable 객체를 수정해서 사용해야 할 경우 새로운 객체를 만들어 사용한다.**

- Int는 immutable이다. Int 값을 수정할 수 있는 이유는 내부적으로 plus와 minus 메서드가 값을 수정한 새로운 객체를 생성하여 return하기 때문이다.
- Iterable도 immutable이다. 때문에 map, filter 메서드로 새로운 객체를 만들어 리턴한다.
- 객체
    - 객체의 속성값이 val인 경우 변경이 불가능하다. 위 방법처럼 값을 변경한 새로운 객체를 생성해야 한다.
        
        ```kotlin
        class Person(
                val age: Int,
                val name: String,
        ) {
            fun newName(name: String) = Person(age, name)
        }
        ```
        
    - data 한정자 사용
        
        data 한정자는 copy라는 이름의 메서드를 사용할 수 있다. 
        copy메서드로 기본 생성자 프로퍼티가 같은 새로운 객체를 만들어 낼 수 있다. 
        
        ```kotlin
        data class Person(
           val age: Int,
           val name: String,
        )
        
        private fun main() {
            var person = Person(27, "dong")
            println(person.hashCode())
            person = person.copy(age = 24)
            println(person.hashCode())
            println(person)
        }
        /**
         * 3090121
         * 3090028
         * Person(age=24, name=dong)
         */
        ```
        
    <br><br>

### ✅ 다른 종류의 변경 가능 지점

- mutable 컬렉션 사용
변경 가능 지점: 리스트 구현 내부 (멀티 스레드 환경에서 내부적 동기화 정확성 부재)
    
    ```kotlin
    val list: MutableList<Int> = mutableListOf()
    ```
    
- mutable 프로퍼티에 읽기 전용 컬렉션 사용
변경 가능 지점: 프로퍼티 자체 변경시
    
    ```kotlin
    val list: List<Int> = listOf()
    ```
    
- 최악의 방법 (mutable 프로퍼티와 mutable 컬렉션을 함께 사용)
이 방법은 프로퍼티와 컬렉션 두 곳 모두 동기화 문제를 관리해야 한다.
    
    ```kotlin
    var list: MutableList<Int> = mutableListOf()
    ```
    
    <br><br>

### ✅ 변경 가능 지점 노출하지 말기

- 방어적 복제(defensive copying)
    - 객체를 복제하여 리턴하자.
    - A class에 있는 private mutable객체를 리턴했을 때 리턴된 객체가 변경될 가능성이 있다. 👎
    이때 copy() 메서드로 객체 복제본을 만들어 리턴하는 것이 좋다. 👍

<br><br>

### ✅ 정리

- var보다는 val을 사용하는 것이 좋다.
- mutable 프로퍼티보다 immutable 프로퍼티를 사용하는 것이 좋다.
- mutable객체와 클래스보다는 immutable객체와 클래스를 사용하는 것이 좋다.
- 변경이 필요한 대상을 만들어야 한다면, immutable 데이터 클래스로 만들고 copy를 활용하는 것이 좋다.
- 컬렉션에 상태를 저장해야 한다면, mutable 컬렉션 보다는 읽기 전용 컬렉션을 사용하는 것이 좋다.
- 변이 지점을 적절하게 설계하고, 불필요한 변이 지점은 만들지 않는 것이 좋다.
- mutable객체를 외부에 노출하지 않는 것이 좋다.