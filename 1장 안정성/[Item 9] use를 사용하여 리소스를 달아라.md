# use를 사용하여 리소스를 달아라

더이상 필요하지 않을 때, close 메서드를 사용해서 명시적으로 닫아야 하는 리소스가 있다. 

- InputStream과 OutputStream
- java.sql.Connection
- java.io.Reader(FileReader, BufferedReader, CSSParser)
- java.new.Socket과 java.util.Scanner

이러한 리소스들은 AutoCloseable을 상속받는 Closeable 인터페이스를 구현하고 있다.

```kotlin
// Closeable
public interface Closeable extends AutoCloseable {
    void close() throws IOException;
}
// AutoCloseable
public interface AutoCloseable {
    void close() throws Exception;
}
```

이러한 모든 리소스는 최종적으로 리소스에 대한 레퍼런스가 없어질 때, 가비지 컬렉터가 처리한다.
하지만 굉장히 느리며 비용이 많이 들어간다. 
따라서 더이상 필요하지 않다면, 명시적으로 close 메서드를 호출해 주는 것이 좋다. 

<br><br>

### ✅ 정통적 방식 try-finally

```kotlin
fun countCharactersInFile(path: String): Int {
    val reader = BufferedReader(FileReader(path))
    try {
        return reader.lineSequence().sumBy { it.length }
    } finally {
        reader.close()
    }
}
```

리소스를 닫을 때 예외가 발생할 수 있는데, 이러한 예외를 따로 처리하지 않기 때문에 좋은 코드가 아니다.

또한 try-finally를 사용하여 처리하면, try 블록이나 finllay 블록 내부에서 오류가 발생하면 둘 중 하나만 전파된다.

<br><br>

### ✅ use 함수

- use 함수는 모든 Closeable 객체에 사용할 수 있다.
- use 함수를 사용하면 Closeable/AutoCloseable을 구현한 객체를 쉽고 안전하게 처리할 수 있다.
- 람다 형태로 사용 가능
- 파일을 한 줄씩 처리할 때 useLines 사용

```kotlin
fun countCharactersInFile(path: String): Int {
    val reader = BufferedReader(FileReader(path))
    reader.use {
        return reader.lineSequence().sumBy { it.length }
    }
}
```