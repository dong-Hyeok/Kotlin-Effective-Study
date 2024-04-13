# 사용자 정의 오류보다는 표준 오류를 사용하라

**제목 처럼 사용자 정의 오류를 사용하기 보단, 가능한 표준 라이브러리를 사용하는 것이 좋다.** 


### ✅ 표준 라이브러리를 권장하는 이유

- 많은 개발자들이 더 쉽고 빠르게 이해할 수 있다. 그러므로 재사용성도 더 좋아진다.

<br>

### ✅ 표준 라이브러리 예외

- `IllegalArgumentException`과 `IllegalStateException` : `require`와 `check`를 사용해 throw 할 수 있는 예외다.
- `IndexOutOfBoundsException` : 인덱스 파라미터의 `값이 범위를 벗어났을때` 나타냅니다. 보통 ArrayList.get(Int)를 사용할 때 throw 된다.
- `ConcurrentModificationException` : `동시 수정`을 금지했는데, 발생했을때 나타낸다.
- `UnSupportedOperationException` : 사용자가 사용하고자 하는 메서드가 현재 객체에서는 사용할 수 없다는 것을 나타낸다.
- `NoSuchElementException` : 사용자가 사용하려고 했던 요소가 존재하지 않음을 나타낸다.

<br>

> `UnSupportedOperationException` 
클래스 내부에 사용할 수 없는 메서드를 일부러 두는 경우도 있다. 예를 들어 listOf에는 사용하면 무조건적으로 오류가 발생하는 메서드들이 있다. 이는 “listOf로 만들어진 컬렉션은 immutable이므로 조작할 수 없습니다.” 라고 명시적으로 알려주기 위한 목적으로 사용된다.
>