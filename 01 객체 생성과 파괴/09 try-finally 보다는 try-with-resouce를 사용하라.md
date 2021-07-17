try-finally 보다는 try-with-resouce를 사용하라
=================================================

# try-finally

몇몇 자바 라이브러리에는 `close()`를 호출해 직접 닫아줘야하는 자원이 많다.       
`InputSream`, `OutputSream`, `java.sql.Connection` 등이 좋은 예시이다.       
**그러나 자원 닫기는 클라이언트가 놓치기 쉬워서 예측할 수 없는 성능 문제로 이어지기도 한다.**       
   
전통적으로 자원이 제대로 닫힘을 보장하는 수단으로 `try-finally`가 쓰였다.      
예외가 발생하거나 메서드에서 반환되는 경우를 포함해서 말이다.     

```java
static String firstLineOfFile(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        return br.readerLine();
    } finally {
        br.close();
    }
}
```
나쁘지는 않지만 이러한 자원을 많이 사용하면 사용할 수록 코드의 가독성을 해치게 된다.   

```java
static void copy(String src, String dst) throws IOException {
    InputStream in = new FileInputStream(src);
    try {
        OutputStream out = new FileOutPutStream(dst);
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while((n = in.read(buf)) >= 0){
                out.write(buf, 0, n);
            }
        } finally {
            out.close();
        }
    } finally {
        in.close();
    }
}
```
이런 `try-finally` 문에는 미묘한 결점이 있는데 예외는 try는 물론 finally에도 발생할 수 있다는 것이다.            
예를 들어, `firstLineOfFile()`안의 `br.readLine()`가 예외를 던지게 된다면 `finally`의 `close`도 실패할 것이다.         
그러면 스택 추적 내역에 첫 번째 예외에 관한 정보는 남지 않게 되어, 실제 시스템에서의 디버깅을 몹시 어렵게한다.      
물론, 이를 해결할 수는 있지만 코드가 매우 지저분해진다는 문제가 동반하게 된다.      

이러한 문제들을 해결하기 위해 JDK7 에서는 try-with-resources를 지원하기 시작했다.   
  
# try-with-resources
**`try-with-resources`를 사용하려면 해당 자원이 `AutoCloseable` 인터페이스를 구현해야한다.**  
그러니 닫아야 하는 자원을 가진 클래스를 작성한다면 AutoCloseable을 반드시 구현하자   

```java
static String fisrtLineOfFile(String path) throws IOException {
    try (BufferedReader br = new Bufferedreader(new FileReader(path))) {
        return br.readLine();
    }
}
```
```java
static void copy(String src, String dst) throws IOException {
    try(InputStream in = new FileInputStream(src);
        OutputStream out = new FileOutputStream(dst)) {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0) {
                out.write(buf, 0, n);
            }
        }
}
```






